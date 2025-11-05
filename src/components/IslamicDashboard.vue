<template>
  <div class="min-h-screen bg-gradient-to-br from-[#8c59d0] via-[#9d6ad6] to-[#7c4cc0] p-4 pb-16 sm:pb-18 md:pb-20 font-poppins flex items-center justify-center">
    <div class="max-w-7xl mx-auto space-y-2 w-full">
      <!-- Top bar -->
      <div class="flex items-start justify-between">
        <div class="w-8 h-8 bg-slate-50/95 backdrop-blur-sm rounded-full border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 flex items-center justify-center">
          <img src="https://api.builder.io/api/v1/image/assets/TEMP/c5184149f0768d5096eb784a36356908c83b617b?width=34" alt="User Profile" class="w-4 h-4" />
        </div>
      </div>

      <!-- Goals + Time + Mobile Calendar -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-4 ">
        <div class="flex items-center justify-center lg:justify-start">
          <div class="w-full bg-slate-50/95 backdrop-blur-sm rounded-2xl border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 px-6 py-4">
            <h2 class="text-[#8c59d0] text-xl font-bold text-center tracking-wider">ЦЕЛИ</h2>
          </div>
        </div>

        <div class="flex items-center justify-center">
          <div class="w-80 bg-slate-50/95 backdrop-blur-sm rounded-2xl border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 px-5 py-4 text-center">
            <div class="text-gray-900 font-extrabold">
              <div class="text-xl leading-tight">{{ formatTime() }}</div>
              <div class="text-xl leading-tight">{{ formatDate(new Date()) }}</div>
            </div>
          </div>
        </div>

        <div class="flex justify-center lg:justify-end">
          <div class="w-full lg:w-96 bg-slate-50/95 backdrop-blur-sm rounded-2xl border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 p-1 overflow-hidden flex flex-col">
            <div class="rounded-2xl bg-[#9d6ad6] text-white text-center font-bold tracking-wide py-1.5 mx-0.5 mb-2 flex items-center justify-between px-2">
              <button @click="previousMonth" class="text-white hover:bg-white/20 rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold">‹</button>
              <span class="font-poppins text-lg">{{ currentMonthName }}</span>
              <button @click="nextMonth" class="text-white hover:bg-white/20 rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold">›</button>
            </div>
            <div class="px-2 flex-1">
              <div class="grid grid-cols-7 text-center text-gray-700 font-semibold text-[11px] border-b border-black/20 pb-1 mb-1">
                <div>П</div><div>В</div><div>С</div><div>Ч</div><div>П</div><div>С</div><div>В</div>
              </div>
              <div class="grid grid-cols-7 gap-y-1 text-center text-gray-900 text-[12px]">
                <div
                  v-for="day in calendarDays"
                  :key="day.date.toISOString()"
                  @click="selectDate(day)"
                  :class="[
                    'cursor-pointer hover:bg-gray-100 rounded p-1 transition-colors',
                    !day.isCurrentMonth && 'text-gray-400',
                    day.isToday && 'bg-blue-100 font-bold',
                    day.isSelected && 'bg-[#9d6ad6] text-white font-bold'
                  ]"
                >
                  {{ day.day }}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Sunnah Bar -->
      <div class="flex justify-center">
        <button type="button" class="mb-8 w-48 rounded-full py-2.5 px-4 text-white font-semibold tracking-wide border-2 border-white/90 bg-[#8c59d0] hover:brightness-110 active:scale-95 transition-all">
          <span class="text-base">Сунна</span>
        </button>
      </div>

      <!-- Prayer Cards -->
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-5 gap-6">
        <div v-for="(p, index) in prayers" :key="p.title" class="w-full max-w-56 flex flex-col mx-auto mb-8">
          <h3 class="text-black text-center font-cairo text-xl font-semibold mb-1">{{ p.title }}</h3>
          <p class="text-black text-center font-cairo text-base font-medium mb-3">{{ p.time }}</p>
          <div :class="`prayer-card-${index} w-full aspect-[9/16] rounded-2xl border-2 border-t-[#323232] border-r-[#323232] border-b-4 border-b-[#323232] border-l-[#323232] relative overflow-hidden pb-2 flex flex-col bg-white`">
            <div class="prayer-card-content h-full overflow-y-auto ">
            <!-- Multiple input sections with animation -->
            <TransitionGroup name="list-item" tag="div">
              <div 
                v-for="(input, inputIndex) in prayerCardStates[index].inputs" 
  :key="input.id" 
  class="list-item-container mb-2"
>
  
  <!-- Блок со свернутым инпутом -->
  <div 
  :class="[
    'collapsed-input-block h-8 mx-2 mt-2 rounded-lg border border-primary flex items-center overflow-hidden transition-all duration-300 ease-in-out',
    input.expanded ? 'collapsed-hidden' : 'collapsed-visible',
    input.isFixed ? 'bg-gray-100' : 'bg-transparent'
  ]"
>
  <input 
    @click="!input.isFixed && expandAndFocusText(index, inputIndex)" 
    :value="input.text" 
    :title="input.text" 
    placeholder="Текст" 
    :class="[
      'text-black border-none outline-none px-2 h-full flex-1 truncate w-full text-sm font-medium font-poppins',
      input.isFixed ? 'cursor-default bg-gray-100 text-center ' : 'cursor-pointer hover:bg-gray-50 bg-transparent'
    ]"
    readonly 
  />
  <div v-if="!input.isFixed || input.time" class="h-full flex items-center">
    <input 
      @click="!input.isFixed && expandAndFocusTime(index, inputIndex)" 
      :value="input.time" 
      placeholder="Время" 
      :class="[
        'border-none outline-none h-full w-auto min-w-[60px] max-w-[110px] text-right placeholder:text-right px-2 text-sm whitespace-nowrap',
        input.isFixed ? 'cursor-default bg-gray-100 text-black' : 'cursor-pointer hover:bg-gray-50 bg-transparent text-black'
      ]"
      readonly 
    />
  </div>
</div>

  <!-- Раскрывающийся блок -->
  <div 
    v-if="!input.isFixed"
    :class="[
      'expandable-block mx-2 mt-0 overflow-hidden transition-all duration-300 ease-in-out',
      input.expanded ? 'expandable-block-open' : 'expandable-block-closed'
    ]"
  >
    <div class="bg-white/10 border border-primary rounded-lg backdrop-blur-[3px] flex flex-col">
      <textarea 
        v-model="input.text"
        @blur="handleExpandedBlur(index, inputIndex)"
        class="w-full resize-none max-h-20 overflow-y-auto break-words text-black p-2 bg-white/10" 
        placeholder="Введите текст"
      ></textarea>
      <input 
        class="text-black bg-white/10 border-none outline-none text-center text-sm p-1" 
        v-model="input.time"
        @blur="handleExpandedBlur(index, inputIndex)"
        @input="handleTimeInput(index, $event, inputIndex)" 
        placeholder="00:00 - 00:00"
      />
    </div>
  </div>

</div>
            </TransitionGroup>

            <div class="mt-4 ml-2">
              <button @click="addNewInput(index)" class="w-8 h-8 rounded-full text-white text-lg flex items-center justify-center bg-primary hover:bg-primary/80 transition-colors">
                <span class="">+</span>
              </button>
            </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Bottom Grid -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-4 items-start">
        <div>
          <div class="w-full min-h-[100%] bg-slate-50/95 backdrop-blur-sm rounded-2xl border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 px-3 py-3 flex flex-col">
            <h3 class="text-[#8c59d0] text-center font-semibold tracking-wide mb-2">Напоминание</h3>
            <div class="w-full h-full">
              <textarea class="w-full min-h-[15rem] h-full bg-transparent outline-none placeholder:text-gray-400 resize-none text-gray-900 p-2" placeholder="Введите напоминание"></textarea>
            </div>
          </div>
        </div>

        <div class="flex items-center justify-center">
          <div class="w-full max-w-[320px] bg-slate-50/95 backdrop-blur-sm rounded-2xl border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 px-3 pt-2 pb-3">
            <h3 class="text-[#8c59d0] text-center font-semibold tracking-wide mb-2">Заметки</h3>
            <div class="space-y-2 notes-section">
              <!-- Expandable note inputs -->
              <div v-for="(note, noteIndex) in notes" :key="noteIndex" class="mb-2 relative">
                <textarea 
                  v-model="note.text"
                  @focus="focusNote(noteIndex)"
                  @blur="blurNote(noteIndex)"
                  :class="[
                    'w-full resize-none overflow-y-auto text-gray-900 bg-white/70 backdrop-blur-sm shadow-inner ring-1 ring-white/50 rounded-lg outline-none px-3 py-2 transition-all duration-300 ease-in-out',
                    note.focused ? 'note-expanded' : 'note-collapsed'
                  ]"
                  placeholder="Введите текст"
                ></textarea>
                
                <!-- Кнопка удаления появляется при фокусе -->
                <Transition name="fade">
                  <button 
                    v-if="note.focused && notes.length > 1"
                    @mousedown.prevent="deleteNote(noteIndex)"
                    class="absolute top-2 right-2 text-red-500 hover:text-red-700 text-sm bg-white/90 rounded-full w-6 h-6 flex items-center justify-center shadow-md transition-all hover:scale-110"
                  >
                    ✕
                  </button>
                </Transition>
              </div>

              <!-- Add new note button -->
              <button
                @click="addNewNote"
                class="w-full h-10 rounded-full bg-[#8c59d0] hover:bg-[#7c4cc0] text-white font-medium transition-colors flex items-center justify-center"
              >
                <span class="text-lg mr-2">+</span>
                Добавить заметку
              </button>
            </div>
          </div>
        </div>

        <div>
          <div class="w-full min-h-52 bg-slate-50/95 backdrop-blur-sm rounded-2xl border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 p-4">
            <h3 class="text-[#8c59d0] text-center font-semibold tracking-wide mb-4">Трекер выполнения</h3>
            <div class="grid grid-cols-[1fr_auto_1fr] gap-4 items-start">
              <div>
                <h2 class="text-center text-gray-900 font-semibold tracking-wide mb-2">Задачи</h2>
                <div class="space-y-1">
                  <div class="text-gray-900">Общие</div>
                  <div class="text-gray-900">Чтение</div>
                  <div class="text-gray-900">Тренировка</div>
                  <div class="text-gray-900">Учёба</div>
                  <div class="text-gray-900">Встреча</div>
                </div>
              </div>
              <div class="w-px bg-gray-300 h-full self-stretch"></div>
              <div>
                <h2 class="text-center text-gray-900 font-semibold tracking-wide mb-2">Поклонения</h2>
                <div class="space-y-1">
                  <div class="text-gray-900">Намаз</div>
                  <div class="text-gray-900">Ратибат</div>
                  <div class="text-gray-900">Тахаджуд</div>
                  <div class="text-gray-900">Чтение Корана</div>
                  <div class="text-gray-900">Сунна намаз</div>
                  <div class="text-gray-900">Азкары</div>
                  <div class="text-gray-900">Дуа</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, nextTick } from 'vue'

interface PrayerCard {
  title: string
  time: string
}

interface Input {
  id: string
  expanded: boolean
  time: string
  text: string
  isFixed: boolean
  isPinned?: boolean
}

const prayers: PrayerCard[] = [
  { title: 'Фаджр', time: '04:00' },
  { title: 'Зухр', time: '11:00' },
  { title: 'Аср', time: '16:00' },
  { title: 'Магриб', time: '19:00' },
  { title: 'Иша', time: '21:00' },
]


const getInitialInputsForPrayer = (prayer: PrayerCard): Input[] => {
  switch(prayer.title) {
    case 'Фаджр':
    case 'Зухр':
      return [
        {
          id: `ratibat-${prayer.title}-${Date.now()}-1`,
          expanded: false,
          time: '',
          text: 'Ратибат',
          isFixed: true
        },
        {
          id: `namaz-${prayer.title}-${Date.now()}-2`,
          expanded: false,
          time: '',
          text: 'НАМАЗ',
          isFixed: true
        },
        {
          id: `azkary-${prayer.title}-${Date.now()}-3`,
          expanded: false,
          time: '',
          text: 'Азкары',
          isFixed: true
        }
      ]
    
    case 'Аср':
    case 'Магриб':
      return [
        {
          id: `namaz-${prayer.title}-${Date.now()}-1`,
          expanded: false,
          time: '',
          text: 'НАМАЗ',
          isFixed: true
        },
        {
          id: `ratibat-${prayer.title}-${Date.now()}-2`,
          expanded: false,
          time: '',
          text: 'Ратибат',
          isFixed: true
        }
      ]
    
    case 'Иша':
      return [
        {
          id: `namaz-${prayer.title}-${Date.now()}-1`,
          expanded: false,
          time: '',
          text: 'НАМАЗ',
          isFixed: true
        },
        {
          id: `ratibat-${prayer.title}-${Date.now()}-2`,
          expanded: false,
          time: '',
          text: 'Ратибат',
          isFixed: true
        },
        {
          id: `tahajjud-${prayer.title}-${Date.now()}-3`,
          expanded: false,
          time: '',
          text: 'Тахаджуд',
          isFixed: true,
          isPinned: true
        }
      ]
    
    default:
      return [
        {
          id: `fixed-${prayer.title}-${Date.now()}`,
          expanded: false,
          time: '',
          text: 'Намаз',
          isFixed: true
        }
      ]
  }
}

const currentDate = ref(new Date())
const selectedDate = ref(new Date())

const prayerCardStates = ref(
  prayers.map(prayer => ({
    inputs: getInitialInputsForPrayer(prayer)
  }))
)

// Notes state
const notes = ref([
  { focused: false, text: '' },
  { focused: false, text: '' },
  { focused: false, text: '' },
  { focused: false, text: '' }
])

const currentMonth = computed(() => currentDate.value.getMonth())
const currentYear = computed(() => currentDate.value.getFullYear())

const monthNames = [
  'ЯНВАРЬ', 'ФЕВРАЛЬ', 'МАРТ', 'АПРЕЛЬ', 'МАЙ', 'ИЮНЬ',
  'ИЮЛЬ', 'АВГУСТ', 'СЕНТЯБРЬ', 'ОКТЯБРЬ', 'НОЯБРЬ', 'ДЕКАБРЬ'
]

const currentMonthName = computed(() => monthNames[currentMonth.value])

const calendarDays = computed(() => {
  const firstDay = new Date(currentYear.value, currentMonth.value, 1)
  const lastDay = new Date(currentYear.value, currentMonth.value + 1, 0)
  const startDate = new Date(firstDay)
  startDate.setDate(startDate.getDate() - firstDay.getDay())
  const days = []
  const endDate = new Date(lastDay)
  endDate.setDate(endDate.getDate() + (6 - lastDay.getDay()))

  for (let d = new Date(startDate); d <= endDate; d.setDate(d.getDate() + 1)) {
    days.push({
      date: new Date(d),
      day: d.getDate(),
      isCurrentMonth: d.getMonth() === currentMonth.value,
      isToday: d.toDateString() === new Date().toDateString(),
      isSelected: d.toDateString() === selectedDate.value.toDateString()
    })
  }
  return days
})

const previousMonth = () => {
  currentDate.value = new Date(currentYear.value, currentMonth.value - 1, 1)
}

const nextMonth = () => {
  currentDate.value = new Date(currentYear.value, currentMonth.value + 1, 1)
}

const formatTimeInput = (value: string) => {
  const digits = value.replace(/\D/g, '')
  let formatted = ''
  if (digits.length >= 1) formatted += digits.slice(0, 2)
  if (digits.length >= 3) formatted += ':' + digits.slice(2, 4)
  if (digits.length >= 5) formatted += ' - ' + digits.slice(4, 6)
  if (digits.length >= 7) formatted += ':' + digits.slice(6, 8)
  return formatted
}

const handleTimeInput = (cardIndex: number, event: Event, inputIndex: number) => {
  const target = event.target as HTMLInputElement
  const formatted = formatTimeInput(target.value)
  prayerCardStates.value[cardIndex].inputs[inputIndex].time = formatted
}

const expandAndFocusText = async (cardIndex: number, inputIndex: number) => {
  if (prayerCardStates.value[cardIndex].inputs[inputIndex].isFixed) return
  
  if (prayerCardStates.value[cardIndex].inputs[inputIndex].expanded) {
    prayerCardStates.value[cardIndex].inputs[inputIndex].expanded = false
    return
  }

  prayerCardStates.value[cardIndex].inputs.forEach((input, index) => {
    if (index !== inputIndex) {
      input.expanded = false
    }
  })

  prayerCardStates.value[cardIndex].inputs[inputIndex].expanded = true
  await nextTick()

  const textareas = document.querySelectorAll(`.prayer-card-${cardIndex} textarea`)
  const targetTextarea = Array.from(textareas).find(ta => 
    ta.closest('.list-item-container') === document.querySelectorAll(`.prayer-card-${cardIndex} .list-item-container`)[inputIndex]
  ) as HTMLTextAreaElement

  if (targetTextarea) {
    targetTextarea.focus()
  }
}

const expandAndFocusTime = async (cardIndex: number, inputIndex: number) => {
  if (prayerCardStates.value[cardIndex].inputs[inputIndex].isFixed) return
  
  if (prayerCardStates.value[cardIndex].inputs[inputIndex].expanded) {
    prayerCardStates.value[cardIndex].inputs[inputIndex].expanded = false
    return
  }

  prayerCardStates.value[cardIndex].inputs.forEach((input, index) => {
    if (index !== inputIndex) {
      input.expanded = false
    }
  })

  prayerCardStates.value[cardIndex].inputs[inputIndex].expanded = true
  await nextTick()

  const expandedBlocks = document.querySelectorAll(`.prayer-card-${cardIndex} .expandable-block-open input[type="text"], .prayer-card-${cardIndex} .expandable-block-open input:not([type])`)
  const targetTimeInput = Array.from(expandedBlocks).find(input => 
    input.closest('.list-item-container') === document.querySelectorAll(`.prayer-card-${cardIndex} .list-item-container`)[inputIndex]
  ) as HTMLInputElement

  if (targetTimeInput) {
    targetTimeInput.focus()
  }
}

const addNewInput = (cardIndex: number) => {
  const prayer = prayers[cardIndex]
  const newInput: Input = {
    id: `new-${Date.now()}-${Math.random()}`,
    expanded: false,
    time: '',
    text: '',
    isFixed: false
  }
  
  if (prayer.title === 'Иша') {
    // Для Иша добавляем перед последним элементом (Тахаджуд)
    const inputs = prayerCardStates.value[cardIndex].inputs
    prayerCardStates.value[cardIndex].inputs.splice(inputs.length - 1, 0, newInput)
  } else {
    // Для остальных добавляем в конец
    prayerCardStates.value[cardIndex].inputs.push(newInput)
  }
}

const handleExpandedBlur = (cardIndex: number, inputIndex: number) => {
  setTimeout(() => {
    const activeElement = document.activeElement
    const container = document.querySelectorAll(`.prayer-card-${cardIndex} .list-item-container`)[inputIndex] as HTMLElement
    
    if (container && (!activeElement || !container.contains(activeElement))) {
      if (prayerCardStates.value[cardIndex]?.inputs[inputIndex]) {
        prayerCardStates.value[cardIndex].inputs[inputIndex].expanded = false
      }
    }
  }, 100)
}

const focusNote = (noteIndex: number) => {
  if (notes.value[noteIndex]) {
    notes.value[noteIndex].focused = true
  }
}

const blurNote = (noteIndex: number) => {
  if (notes.value[noteIndex]) {
    notes.value[noteIndex].focused = false
  }
}

const deleteNote = (noteIndex: number) => {
  if (notes.value.length > 1) {
    notes.value.splice(noteIndex, 1)
  }
}

const addNewNote = () => {
  notes.value.push({ focused: false, text: '' })
}

interface CalendarDay {
  date: Date
  day: number
  isCurrentMonth: boolean
  isToday: boolean
  isSelected: boolean
}

const selectDate = (day: CalendarDay) => {
  selectedDate.value = new Date(day.date)
}

const formatDate = (date: Date) => {
  return date.toLocaleDateString('ru-RU', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric'
  })
}

const formatTime = () => {
  const now = new Date()
  return now.toLocaleTimeString('ru-RU', {
    hour: '2-digit',
    minute: '2-digit'
  })
}

onMounted(() => {
  setInterval(() => {
    // Force reactivity update for time
  }, 60000)
})
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700;800&display=swap');

.font-poppins {
  font-family: 'Poppins', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
}

.list-item-enter-active,
.list-item-leave-active {
  transition: all 0.5s ease;
}

.list-item-enter-from,
.list-item-leave-to {
  opacity: 0;
  transform: translateY(-20px);
  max-height: 0;
  margin-bottom: 0;
  overflow: hidden;
}

.list-item-enter-to,
.list-item-leave-from {
  max-height: 150px;
}

.expandable-content {
  max-height: 0;
  opacity: 0;
  overflow: hidden;
  transition: max-height 0.4s ease-out, opacity 0.3s ease;
}

.expandable-content.expanded {
  max-height: 120px;
  opacity: 1;
}

/* Стили для заметок */
.note-collapsed {
  height: 2.5rem;
  min-height: 2.5rem;
  max-height: 2.5rem;
  line-height: 1.5rem;
}

.note-expanded {
  height: 5.5rem;
  min-height: 5.5rem;
  max-height: 5.5rem;
}

/* Анимация для кнопки удаления */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.expandable-block {
  transition: max-height 0.35s cubic-bezier(0.4, 0, 0.2, 1), 
              opacity 0.3s ease-in-out, 
              margin-top 0.35s cubic-bezier(0.4, 0, 0.2, 1);
}

.expandable-block-closed {
  max-height: 0;
  opacity: 0;
  pointer-events: none;
}

.expandable-block-open {
  max-height: 250px;
  opacity: 1;
}

/* Анимация для свернутого блока */
.collapsed-input-block {
  transition: max-height 0.35s cubic-bezier(0.4, 0, 0.2, 1),
              opacity 0.3s ease-in-out,
              margin 0.35s cubic-bezier(0.4, 0, 0.2, 1);
}

.collapsed-visible {
  max-height: 2rem;
  opacity: 1;
}

.collapsed-hidden {
  max-height: 0;
  opacity: 0;
  margin-top: 0 !important;
  margin-bottom: 0 !important;
  pointer-events: none;
}

/* Анимация для раскрывающегося блока */
.expandable-block {
  transition: max-height 0.35s cubic-bezier(0.4, 0, 0.2, 1), 
              opacity 0.3s ease-in-out, 
              margin-top 0.35s cubic-bezier(0.4, 0, 0.2, 1);
}

.expandable-block-closed {
  max-height: 0;
  opacity: 0;
  pointer-events: none;
}

.expandable-block-open {
  max-height: 250px;
  opacity: 1;
}

[class*="prayer-card-content"] {
  scrollbar-width: thin;
  scrollbar-color: #8C59D0 transparent; /* Синий полупрозрачный */
}

[class*="prayer-card-content"]::-webkit-scrollbar {
  width: 4px; /* Тонкий скроллбар */
}

[class*="prayer-card-content"]::-webkit-scrollbar-track {
  background: transparent;
  margin: 8px 0;
}

[class*="prayer-card-content"]::-webkit-scrollbar-thumb {
  background: #8C59D0; /* Синий полупрозрачный */
  border-radius: 10px;
}

[class*="prayer-card-content"]::-webkit-scrollbar-thumb:hover {
  background: rgba(59, 130, 246, 0.8); /* Ярче при наведении */
}

[class*="prayer-card-content"]::-webkit-scrollbar-button {
  display: none;
}
</style>