<template>
  <ion-page>
    <ion-header translucent>
      <ion-toolbar>
        <ion-title>NFC Scanner</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content fullscreen class="ion-padding">
      <ion-button expand="block" @click="startRead">Lesen starten</ion-button>
      <ion-button expand="block" @click="startWrite">Schreiben starten</ion-button>

      <ion-modal :is-open="scanning" backdrop-dismiss="false">
        <ion-content class="ion-padding ion-text-center">
          <p>NFC Scan läuft...</p>
          <ion-button color="danger" @click="cancelScan">Abbrechen</ion-button>
        </ion-content>
      </ion-modal>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { Nfc, NfcUtils } from '@capawesome-team/capacitor-nfc';

const scanning = ref(false);
const router = useRouter();

const checkNfcStatus = async (): Promise<boolean> => {
  const { isEnabled } = await Nfc.isEnabled();
  if (!isEnabled) {
    alert('NFC ist deaktiviert. Bitte aktiviere NFC in den Einstellungen.');
    await Nfc.openSettings();
    return false;
  }
  return true;
};

const clearListeners = async () => {
  await Nfc.removeAllListeners();
};

const startRead = async () => {
  if (!(await checkNfcStatus())) return;

  scanning.value = true;

  await clearListeners();

  Nfc.addListener('nfcTagScanned', async (event) => {
    scanning.value = false;
    await Nfc.stopScanSession();
    await clearListeners();

    // Navigiere zur Detailseite mit Tag-Daten als JSON stringified
    router.push({ name: 'Detail', params: { tag: JSON.stringify(event.nfcTag) } });
  });

  await Nfc.startScanSession();
};

const startWrite = async () => {
  if (!(await checkNfcStatus())) return;

  const textToWrite = prompt('Bitte Text eingeben, der auf den Tag geschrieben werden soll:', 'Hallo NFC');
  if (!textToWrite) return;

  scanning.value = true;

  await clearListeners();

  const utils = new NfcUtils();
  const { record } = utils.createNdefTextRecord({ text: textToWrite });

  Nfc.addListener('nfcTagScanned', async () => {
    try {
      await Nfc.write({ message: { records: [record] } });
      alert('Schreiben erfolgreich!');
    } catch (e) {
      alert('Fehler beim Schreiben: ' + e);
    }
    scanning.value = false;
    await Nfc.stopScanSession();
    await clearListeners();
  });

  await Nfc.startScanSession();
};

const cancelScan = async () => {
  scanning.value = false;
  await Nfc.stopScanSession();
  await clearListeners();
};
</script>

<style scoped>
ion-content {
  --padding-top: 20px;
}
</style>

<style scoped>
#container {
  text-align: center;
  
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  
  color: #8c8c8c;
  
  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
