<template>
  <section id="metodo">
    <div class="wrap">
      <div class="sez-eyebrow"><span class="etichetta">COME LAVORIAMO</span></div>
      <h2 class="sez-h2">Dalla prima chiamata al sito online</h2>
      <p class="sez-intro">Quattro tappe, in ordine: non iniziamo mai dal design prima di aver capito da dove arrivano oggi le vostre prenotazioni.</p>

      <div class="metodo">
        <div class="tappa" v-for="t in tappe" :key="t.idx">
          <div class="idx">{{ t.idx }}</div>
          <div class="corpo">
            <h3>{{ t.titolo }}</h3>
            <p>{{ t.testo }}</p>
            <span class="durata">{{ t.durata }}</span>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
const tappe = [
  {
    idx: '01',
    titolo: 'Ascolto e numeri',
    testo: 'Guardiamo insieme da dove arrivano le prenotazioni oggi — OTA, diretto, telefono — e quali mesi soffrono di più. Da lì nascono gli obiettivi reali del progetto.',
    durata: '1–2 settimane'
  },
  {
    idx: '02',
    titolo: 'Direzione artistica',
    testo: "Costruiamo l'identità visiva del sito attorno a ciò che rende unico l'hotel: la luce, i materiali, la storia del posto. Non un tema, un progetto.",
    durata: '2–3 settimane'
  },
  {
    idx: '03',
    titolo: 'Sviluppo e motore offerte',
    testo: 'Costruiamo il sito e il pannello di gestione offerte, collegato al vostro sistema di prenotazione esistente. Voi lo provate mentre lo finiamo.',
    durata: '4–6 settimane'
  },
  {
    idx: '04',
    titolo: 'Online e calendario social',
    testo: 'Il sito va live, e parte il calendario dei contenuti stagionali. Restiamo al fianco del team per i primi mesi, poi passiamo la gestione a voi — o continuiamo insieme.',
    durata: 'continuativo'
  }
]
</script>
