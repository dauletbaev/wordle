<script setup lang="ts">
const infoPopup = ref(true)
const isOpen = ref(false)
const word = ref('')

const closeModal = (e?: KeyboardEvent) => {
  if (e && e.code === 'Escape' && isOpen.value) {
    isOpen.value = false
    e.preventDefault()
    return
  }

  if (!e) {
    isOpen.value = false
  }
}

const createUri = () => {
  if (word.value.length !== 5) {
    alert('Bes háripli sóz kiritiń')
    return
  }

  const encodedWord = btoa(word.value)
  const url = `https://sozle.qaraqalpaq.org/?w=${encodeURIComponent(encodedWord)}`

  useFetch('/api/word', {
    method: 'POST',
    body: { word: word.value, score: 0.3 }
  })

  navigator.clipboard.writeText(url)
  closeModal()
  useTrackEvent('share-word', { props: { word: word.value } })
  alert('Nusqalap alındı')
}

const understandInitalPopUp = () => {
  infoPopup.value = false
  useTrackEvent('understood')
}

onMounted(() => {
  window.addEventListener('keydown', closeModal)
})
onUnmounted(() => {
  window.removeEventListener('keydown', closeModal)
})
</script>


<template>
  <pop-up>
    <div v-if="infoPopup" :class="$style.modal">
      <teleport to="body">
        <div @click="infoPopup = false" id="backdrop" />
      </teleport>
      <p>Sálem bul sózle oyını</p>

      <p>Oyın shártleri júdá ápiwayı 5 háripli sóz jasırılǵan.<br />Jasırılǵan sózde tabıw ushın sizde 6 imkániyat
        bar.<br />Óziń oylaǵan sózdi kiritip kóresiz eger klaviaturada 🟩 reńge ózgerse siz tuwra joldasız bul háripti
        tawdıńız, eger 🟨 reńde bolsa bul háripte sol sózde bar biraq ornı almasıp turıptı.<br />Al eger ⬜ bolsa bul
        hárip jasırılǵan sózde qollanılmaǵan.</p>


      <p :class="$style.divider">Oyındı jánede qızıqlı etiw maqsetinde ózińiz sóz qosıp doslarıńızǵa jiberip oynasańızda
        boladı.</p>


      <p><span style="color:salmon">Esletpe</span>: Siz bul oyındı oynap qaraqalpaq tiliniń rawajlanıwına óz úlesińizdi
        qosıwıńız múmkin. Sol sebepli ilájı barınsha haqıyqattan da bar bolǵan sózlerdi kiritiwińizdi sorap qalamız.</p>

      <button @click="understandInitalPopUp">Túsindim</button>
    </div>
  </pop-up>
  <pop-up>
    <div v-if="isOpen" :class="[$style.modal, $style['center-vertical']]">
      <teleport to="body">
        <div @click="isOpen = false" id="backdrop" />
      </teleport>
      <p>Sózdi kiritiń</p>
      <input v-model="word" type="text" placeholder="Mısalı: sálem" maxlength="5" minlength="5" />
      <button @click="createUri">Úlesiw</button>
    </div>
  </pop-up>
  <header>
    <span @click="isOpen = !isOpen" :class="$style.custom">Ózim sóz qosıp<br /> oynaymaqshıman</span>
    <h1>SÓZLE</h1>
  </header>
</template>

<style module>
.custom {
  cursor: pointer;
}

.divider {
  margin: 3rem 0;
}

.modal {
  position: absolute;
  min-width: 40vw;
  left: 50%;
  top: 80px;
  color: #fff;
  background-color: rgba(0, 0, 0, 0.85);
  padding: 16px 20px;
  z-index: 2;
  border-radius: 4px;
  transform: translateX(-50%);
  font-weight: 600;
}

.modal button {
  width: 30%;
  padding: 10px;
  height: auto !important;
  margin: 5px auto !important;
  margin-top: 15px !important;
}

@media (max-width: 600px) {
  .modal {
    width: 80%;
    top: 10px;
    padding: 8px 12px;
  }

  .center-vertical {
    top: 35%;
  }

  .divider {
    margin: 12px 0;
  }
}
</style>
