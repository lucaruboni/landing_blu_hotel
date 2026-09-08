<template>
  <div class="hero-pin" ref="pinEl">
    <div class="hero-sticky" ref="stickyEl">
      <div class="hero-topbar">
        <span class="etichetta">BLU · STUDIO CREATIVO PER HOTEL INDIPENDENTI</span>
        <span class="etichetta hero-hint">TRASCINA · INCLINA IL TELEFONO · SCORRI</span>
      </div>

      <div class="hero-canvas-wrap"><canvas ref="canvasEl" id="dots" aria-hidden="true"></canvas></div>

      <div class="hero-progress">
        <i :class="{ attiva: stage === 0 }"></i>
        <i :class="{ attiva: stage === 1 }"></i>
        <i :class="{ attiva: stage === 2 }"></i>
      </div>

      <div class="hero-stage">
        <div class="hero-headline">
          <div class="riga" :class="{ attiva: stage === 0 }">
            <h1>Ogni prenotazione su Booking costa il 15–20%.</h1>
            <p class="nota">Su un hotel da 40 camere sono decine di migliaia di euro l'anno che restano fuori dalla vostra cassa, ogni stagione.</p>
          </div>
          <div class="riga" :class="{ attiva: stage === 1 }">
            <h1>Il vostro sito può prendersele indietro.</h1>
            <p class="nota">Non un template da agenzia di siti per hotel: un progetto disegnato sulla vostra struttura, che porta chi vi ha già trovato a prenotare direttamente da voi.</p>
          </div>
          <div class="riga" :class="{ attiva: stage === 2 }">
            <h1>BLU. Siti e contenuti che vendono per l'hotel.</h1>
            <p class="nota">Sito su misura, motore di offerte stagionali, post pensati mese per mese sulle vostre peculiarità. Continuate a scorrere per vedere come lavoriamo.</p>
          </div>
        </div>
      </div>

      <div class="hero-scrollhint"><span>SCORRI</span><span class="linea"></span></div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

const pinEl = ref(null)
const stickyEl = ref(null)
const canvasEl = ref(null)
const stage = ref(0)

let ctx, W, H, DPR
let RINGS = 60, PER_RING = 46, N = RINGS * PER_RING
const TOWER_H = 400, FOOT_R = 190, TWIST_PER_RING = 0.062, SQ_EXP = 3.4
let scattered = [], ordered = []

const reduced = typeof window !== 'undefined' && window.matchMedia('(prefers-reduced-motion: reduce)').matches

const interact = { yaw: 0, pitch: 0, yawT: 0, pitchT: 0, tiltYaw: 0, tiltPitch: 0, tiltYawT: 0, tiltPitchT: 0 }
let orientationAsked = false
let tiltBase = null
let lastProgress = 0
let lastStage = -1
let startTime = 0
let rafId = null

function buildGeometry(){
  scattered = []; ordered = []
  for (let ring = 0; ring < RINGS; ring++){
    const v = ring / (RINGS - 1)
    const y = (v - 0.5) * TOWER_H
    const twist = ring * TWIST_PER_RING
    const floorR = FOOT_R * (1 - 0.16 * Math.sin(v * Math.PI))
    for (let k = 0; k < PER_RING; k++){
      const a = (k / PER_RING) * Math.PI * 2
      const cx = Math.sign(Math.cos(a)) * Math.pow(Math.abs(Math.cos(a)), 2 / SQ_EXP)
      const cz = Math.sign(Math.sin(a)) * Math.pow(Math.abs(Math.sin(a)), 2 / SQ_EXP)
      const rx = cx * floorR, rz = cz * floorR
      const ox = rx * Math.cos(twist) - rz * Math.sin(twist)
      const oz = rx * Math.sin(twist) + rz * Math.cos(twist)
      ordered.push({ x: ox, y, z: oz })

      const jr = Math.random()
      scattered.push({
        x: ox + (Math.random() - 0.5) * 300 * jr,
        y: y + (Math.random() - 0.5) * 170,
        z: oz + (Math.random() - 0.5) * 300 * jr
      })
    }
  }
}

function resize(){
  const canvas = canvasEl.value
  if (!canvas) return
  DPR = Math.min(window.devicePixelRatio || 1, 2)
  W = canvas.clientWidth; H = canvas.clientHeight
  canvas.width = W * DPR; canvas.height = H * DPR
  ctx.setTransform(DPR, 0, 0, DPR, 0, 0)
}

function onPointerMove(e){
  const rect = canvasEl.value.getBoundingClientRect()
  const nx = ((e.clientX - rect.left) / rect.width) * 2 - 1
  const ny = ((e.clientY - rect.top) / rect.height) * 2 - 1
  interact.yawT = Math.max(-1, Math.min(1, nx)) * 0.55
  interact.pitchT = Math.max(-1, Math.min(1, ny)) * -0.32
}
function resetPointer(){ interact.yawT = 0; interact.pitchT = 0 }

function askOrientation(){
  if (orientationAsked) return
  orientationAsked = true
  if (typeof DeviceOrientationEvent !== 'undefined' && typeof DeviceOrientationEvent.requestPermission === 'function'){
    DeviceOrientationEvent.requestPermission().then(state => {
      if (state === 'granted') window.addEventListener('deviceorientation', onTilt)
    }).catch(() => {})
  } else if (window.DeviceOrientationEvent) {
    window.addEventListener('deviceorientation', onTilt)
  }
}
function onTilt(e){
  if (e.beta === null || e.gamma === null) return
  if (!tiltBase) tiltBase = { beta: e.beta, gamma: e.gamma }
  const dGamma = Math.max(-40, Math.min(40, e.gamma - tiltBase.gamma))
  const dBeta = Math.max(-40, Math.min(40, e.beta - tiltBase.beta))
  interact.tiltYawT = (dGamma / 40) * 0.6
  interact.tiltPitchT = (dBeta / 40) * -0.4
}

function draw(t, rotY, rotX){
  ctx.clearRect(0, 0, W, H)
  const cy = Math.cos(rotY), sy = Math.sin(rotY)
  const cx = Math.cos(rotX), sx = Math.sin(rotX)
  const cxp = W / 2, cyp = H * 0.54
  const scale = Math.min(1, W / 760)
  const pts = []
  for (let i = 0; i < N; i++){
    const b = scattered[i], g = ordered[i]
    const x = (b.x + (g.x - b.x) * t) * scale
    const y = (b.y + (g.y - b.y) * t) * scale
    const z = (b.z + (g.z - b.z) * t) * scale

    const x1 = x * cy - z * sy, z1 = x * sy + z * cy
    const y1 = y * cx - z1 * sx, z2 = y * sx + z1 * cx

    const persp = 720 / (720 + z2)
    pts.push({ sx: cxp + x1 * persp, sy: cyp + y1 * persp, s: persp, z: z2 })
  }
  pts.sort((a, b) => a.z - b.z)
  for (const p of pts){
    const alpha = 0.26 + 0.58 * ((p.z + 300) / 600)
    const rad = Math.max(0.8, 1.55 * p.s)
    ctx.beginPath()
    ctx.fillStyle = 'rgba(11,11,11,' + Math.min(1, Math.max(0.12, alpha)) + ')'
    ctx.arc(p.sx, p.sy, rad, 0, Math.PI * 2)
    ctx.fill()
  }
}

function render(progress, now){
  const idle = reduced ? 0 : (now - startTime) / 1000 * 0.06
  if (!reduced){
    interact.yaw += (interact.yawT - interact.yaw) * 0.07
    interact.pitch += (interact.pitchT - interact.pitch) * 0.07
    interact.tiltYaw += (interact.tiltYawT - interact.tiltYaw) * 0.08
    interact.tiltPitch += (interact.tiltPitchT - interact.tiltPitch) * 0.08
  }
  const rotY = (reduced ? 0.5 : progress * 2.4 + idle) + interact.yaw + interact.tiltYaw
  const rotX = -0.2 - (reduced ? 0 : progress * 0.08) + interact.pitch + interact.tiltPitch
  const morph = reduced ? 1 : Math.min(1, progress * 1.4)
  draw(morph, rotY, rotX)
}

function onScroll(){
  let progress = 0
  if (!reduced && pinEl.value){
    const rect = pinEl.value.getBoundingClientRect()
    const span = rect.height - window.innerHeight
    progress = span > 0 ? Math.min(1, Math.max(0, -rect.top / span)) : 0
  } else {
    progress = 1
  }
  const s = progress < 0.32 ? 0 : progress < 0.66 ? 1 : 2
  if (s !== lastStage){ stage.value = s; lastStage = s }
  lastProgress = progress
}

function loop(now){
  render(lastProgress, now)
  rafId = requestAnimationFrame(loop)
}

function onResize(){ resize(); onScroll() }

onMounted(() => {
  ctx = canvasEl.value.getContext('2d')
  buildGeometry()
  startTime = performance.now()

  const sticky = stickyEl.value
  if (sticky){
    sticky.addEventListener('pointermove', onPointerMove)
    sticky.addEventListener('pointerleave', resetPointer)
    sticky.addEventListener('pointerup', resetPointer)
    sticky.addEventListener('pointerdown', askOrientation, { once: true })
    sticky.addEventListener('touchstart', askOrientation, { once: true, passive: true })
  }

  window.addEventListener('resize', onResize)
  window.addEventListener('scroll', onScroll, { passive: true })
  resize()
  onScroll()
  rafId = requestAnimationFrame(loop)
})

onBeforeUnmount(() => {
  cancelAnimationFrame(rafId)
  window.removeEventListener('resize', onResize)
  window.removeEventListener('scroll', onScroll)
  window.removeEventListener('deviceorientation', onTilt)
})
</script>
