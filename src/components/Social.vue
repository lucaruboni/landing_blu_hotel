<template>
  <section id="social">
    <div class="wrap">
      <div class="sez-eyebrow"><span class="etichetta">CONTENUTI</span></div>
      <div class="social-intro">
        <div>
          <h2 class="sez-h2">Un calendario editoriale, non post improvvisati</h2>
          <p class="sez-intro">Ogni post nasce da un momento preciso dell'anno dell'hotel e porta a un'offerta viva sul sito — mai contenuto fine a se stesso.</p>
        </div>
        <div class="calendario">CALENDARIO TIPO · 12 MESI</div>
      </div>

      <div class="griglia-social">
        <div class="post" v-for="p in post" :key="p.quando">
          <span class="quando">{{ p.quando }}</span>
          <div class="titolo">{{ p.titolo }}</div>
          <p class="desc">{{ p.desc }}</p>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
const post = [
  {
    quando: 'Marzo',
    titolo: 'Riapertura del giardino',
    desc: 'Racconto per immagini della prima fioritura, collegato al pacchetto "Primavera in giardino" appena pubblicato sul sito.'
  },
  {
    quando: 'Giugno',
    titolo: 'La cucina del territorio',
    desc: 'Serie video brevi con lo chef sui piatti stagionali, per differenziare l\'hotel da chi vende solo "camera con colazione".'
  },
  {
    quando: 'Settembre',
    titolo: 'Vendemmia e bassa stagione',
    desc: "Contenuto pensato per riempire le settimane più deboli, con link diretto all'offerta vendemmia sul motore prenotazioni."
  },
  {
    quando: 'Dicembre',
    titolo: 'Il capodanno in tenuta',
    desc: "Anteprima degli addobbi e del menù, pubblicata tre settimane prima dell'apertura vendite del pacchetto di fine anno."
  }
]
</script>
