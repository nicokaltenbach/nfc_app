<template>
  <ion-page>
    <ion-header translucent>
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-back-button default-href="/nfc-scan" />
        </ion-buttons>
        <ion-title>Tag Details</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <div v-if="tag">
        <p><strong>ID:</strong> {{ tag.id }}</p>
        <p><strong>Writable:</strong> {{ tag.isWritable }}</p>
        <p><strong>Payload:</strong> {{ payloadText }}</p>
        <p><strong>Hex:</strong> {{ utils.convertBytesToHex(utils.convertStringToBytes({text: payloadText})).hex }}</p>

        <ion-button expand="block" @click="copyPayload">Payload kopieren</ion-button>
        <ion-button expand="block" @click="sharePayload">Payload teilen</ion-button>
      </div>
      <div v-else>
        <p>Keine Tag-Daten vorhanden.</p>
      </div>
      <ion-button expand="block" color="medium" @click="goBack">
        Zurück zur Startseite
      </ion-button>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { Clipboard } from '@capacitor/clipboard';
import { Share } from '@capacitor/share';
import { NfcTag, NfcUtils } from '@capawesome-team/capacitor-nfc';

const route = useRoute();
const router = useRouter();
let tag = null as NfcTag | null;
const utils = new NfcUtils();

if (route.query.tag) {
  try {
    console.log("Tag-Daten empfangen: ", route.query.tag);
    tag = JSON.parse(route.query.tag as string);
  } catch {
    tag = null;
  }
}

// NDEF-Record mit Payload (erster Record)
const payloadRecord = tag?.message?.records?.[0];
const payloadText = payloadRecord && utils.getTextFromNdefTextRecord({record: payloadRecord})?.text || "";

const copyPayload = async () => {
  if (!payloadText) return;
  await Clipboard.write({ string: payloadText });
  alert('Payload in Zwischenablage kopiert!');
};

const sharePayload = async () => {
  if (!payloadText) return;
  await Share.share({
    title: 'NFC Payload',
    text: payloadText,
  });
};

const goBack = () => {
  if (window.history.length > 1) {
    router.back();
  } else {
    router.push('/nfc-scan'); // Fallback-Route anpassen
  }
};

</script>