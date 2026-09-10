<template>
  <div class="min-h-screen bg-gradient-to-br from-[#8c59d0] via-[#9d6ad6] to-[#7c4cc0] p-4 pb-16 sm:pb-18 md:pb-20 font-poppins flex items-center justify-center">
    <div class="max-w-[1360px] mx-auto space-y-2 w-full">
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
              <button @click="previousMonth" aria-label="Предыдущий месяц" class="text-white hover:bg-white/20 rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold">‹</button>
              <span class="font-poppins text-lg">{{ currentMonthName }}</span>
              <button @click="nextMonth" aria-label="Следующий месяц" class="text-white hover:bg-white/20 rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold">›</button>
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
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-5 gap-4 xl:gap-3">
        <div v-for="(p, index) in prayers" :key="p.title" class="w-full max-w-[16rem] flex flex-col mx-auto mb-2">
          <h3 class="text-black text-center font-cairo text-xl font-semibold mb-1">{{ p.title }}</h3>
          <p class="text-black text-center font-cairo text-base font-medium mb-3">{{ p.time }}</p>
          <div :class="`prayer-card-${index} w-full aspect-[9/16] rounded-2xl border-2 border-t-[#323232] border-r-[#323232] border-b-4 border-b-[#323232] border-l-[#323232] relative overflow-hidden pb-2 flex flex-col bg-white`">
            <div class="prayer-card-content h-full overflow-y-auto pt-2 ">
            <!-- Multiple input sections with animation -->
            <TransitionGroup name="list-item" tag="div">
              <div 
                v-for="(row, rowIndex) in cardRows(index)" 
  :key="rowKey(row)" 
  class="list-item-container relative mb-2"
>
  
  <!-- Блок со свернутым инпутом -->
  <div @click="onRowClick(index, row, $event)"
  :class="[
    'collapsed-input-block h-8 mx-2 mt-2 rounded-lg border border-primary flex items-center overflow-hidden transition-all duration-300 ease-in-out cursor-pointer select-none',
    isRowExpanded(index, row) ? 'collapsed-hidden' : 'collapsed-visible',
    row.kind === 'fixed' ? 'bg-gray-100' : 'bg-transparent'
  ]"
>
  <input 
    :value="rowText(row)" 
    :title="rowText(row)" 
    placeholder="Текст" 
    :class="[
      'text-black border-none outline-none px-2 h-full flex-1 truncate w-full text-sm font-medium font-poppins',
      row.kind === 'fixed' ? 'cursor-pointer bg-gray-100 text-center' : 'cursor-pointer hover:bg-gray-50 bg-transparent',
      (rowStatus(row) === 'done' || rowStatus(row) === 'pending') ? 'line-through' : ''
    ]"
    readonly 
  />
  <div v-if="row.kind === 'task' || rowTime(row)" class="h-full flex items-center">
    <input 
      :value="rowTime(row)" 
      placeholder="Время" 
      :class="[
        'border-none outline-none h-full w-auto min-w-[60px] max-w-[110px] text-right placeholder:text-right px-2 text-sm whitespace-nowrap',
        row.kind === 'fixed' ? 'cursor-pointer bg-gray-100 text-black' : 'cursor-pointer hover:bg-gray-50 bg-transparent text-black'
      ]"
      readonly 
    />
  </div>
</div>

    <!-- Кружок статуса: завершить / вернуть задачу (правки N1, N3) -->
  <button
    v-show="row.kind === 'fixed' || !isRowExpanded(index, row)"
    v-if="rowStatus(row) !== 'none'"
    type="button"
    @click.stop="cycleStatus(row)"
    :aria-label="statusTitle(row)"
    :title="statusTitle(row)"
    :class="['task-status-badge', statusClass(row)]"
  >
    <span v-if="rowStatus(row) === 'done'" aria-hidden="true">✓</span>
    <span v-else aria-hidden="true">✕</span>
  </button>
  <!-- Раскрывающийся блок -->
  <div 
    v-if="row.kind === 'task'"
    :class="[
      'expandable-block mx-2 mt-0 overflow-hidden transition-all duration-300 ease-in-out',
      isRowExpanded(index, row) ? 'expandable-block-open' : 'expandable-block-closed'
    ]"
  >
    <div class="bg-white/10 border border-primary rounded-lg backdrop-blur-[3px] flex flex-col">
      <textarea 
        :value="rowText(row)" @input="onTaskTextInput(row, $event)"
        @blur="handleExpandedBlur(index, row, $event)"
        class="w-full resize-none max-h-20 overflow-y-auto break-words text-black p-2 bg-white/10" 
        placeholder="Введите текст"
      ></textarea>
      <input 
        class="text-black bg-white/10 border-none outline-none text-center text-sm p-1" 
        :value="rowTime(row)"
        @blur="handleExpandedBlur(index, row, $event)"
        @input="handleTimeInput(row, $event)" 
        placeholder="00:00 - 00:00"
      />
    </div>
  </div>

</div>
            </TransitionGroup>

            <div class="mt-4 ml-2">
              <button @click="addNewInput(index)" :aria-label="`Добавить элемент в ${p.title}`" class="w-8 h-8 rounded-full text-white text-lg flex items-center justify-center bg-primary hover:bg-primary/80 transition-colors">
                <span class="">+</span>
              </button>
            </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Bottom Grid -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-3 items-stretch">
        <div class="h-full">
          <div class="w-full bg-slate-50/95 backdrop-blur-sm rounded-2xl border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 px-4 py-4 flex flex-col bottom-panel">
            <h3 class="text-[#8c59d0] text-center font-semibold tracking-wide mb-2">Напоминание</h3>
            <div class="w-full h-full">
              <textarea class="w-full h-full min-h-0 bg-transparent outline-none placeholder:text-gray-400 resize-none text-gray-900 p-2" placeholder="Введите напоминание" aria-label="Напоминание"></textarea>
            </div>
          </div>
        </div>

        <div class="h-full">
          <div class="w-full bg-slate-50/95 backdrop-blur-sm rounded-2xl border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 px-4 py-4 flex flex-col bottom-panel">
            <h3 class="text-[#8c59d0] text-center font-semibold tracking-wide mb-2">Заметки</h3>
            <div class="flex flex-col min-h-0 flex-1">
              <div class="space-y-2 notes-section notes-scroll flex-1 min-h-0 overflow-y-auto pr-1">
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
                    :aria-label="`Заметка ${noteIndex + 1}`"
                  ></textarea>
                  
                  <!-- Кнопка удаления появляется при фокусе -->
                  <Transition name="fade">
                    <button 
                      v-if="note.focused && notes.length > 1"
                      @mousedown.prevent="deleteNote(noteIndex)"
                      :aria-label="`Удалить заметку ${noteIndex + 1}`"
                      class="absolute top-2 right-2 text-red-500 hover:text-red-700 text-sm bg-white/90 rounded-full w-6 h-6 flex items-center justify-center shadow-md transition-all hover:scale-110"
                    >
                      ✕
                    </button>
                  </Transition>
                </div>
              </div>

              <!-- Add new note button -->
              <button
                @click="addNewNote"
                aria-label="Добавить заметку"
                class="w-full h-10 rounded-full bg-[#8c59d0] hover:bg-[#7c4cc0] text-white font-medium transition-colors flex items-center justify-center shrink-0 mt-2"
              >
                <span class="text-lg mr-2">+</span>
                Добавить заметку
              </button>
            </div>
          </div>
        </div>

        <div class="h-full">
          <div class="w-full bg-slate-50/95 backdrop-blur-sm rounded-2xl border-2 border-slate-200/50 shadow-2xl hover:shadow-3xl transition-all duration-500 p-4 flex flex-col bottom-panel">
            <h3 class="text-[#8c59d0] text-center font-semibold tracking-wide mb-4">Трекер выполнения</h3>
            <div class="grid grid-cols-[1fr_auto_1fr] gap-4 items-start flex-1 min-h-0 overflow-hidden">
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
  status: TaskStatus
  isPinned?: boolean
}
type TaskStatus = 'none' | 'pending' | 'done'

interface Task {
  id: string
  text: string
  time: string
  start: number | null
  end: number | null
  status: TaskStatus
  draftCard: number | null
}

interface CardRowFixed {
  kind: 'fixed'
  input: Input
}

interface CardRowTask {
  kind: 'task'
  task: Task
}

type CardRow = CardRowFixed | CardRowTask


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
          isFixed: true,
          status: 'none'
        },
        {
          id: `namaz-${prayer.title}-${Date.now()}-2`,
          expanded: false,
          time: '',
          text: 'НАМАЗ',
          isFixed: true,
          status: 'none'
        },
        {
          id: `azkary-${prayer.title}-${Date.now()}-3`,
          expanded: false,
          time: '',
          text: 'Азкары',
          isFixed: true,
          status: 'none'
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
          isFixed: true,
          status: 'none'
        },
        {
          id: `ratibat-${prayer.title}-${Date.now()}-2`,
          expanded: false,
          time: '',
          text: 'Ратибат',
          isFixed: true,
          status: 'none'
        }
      ]
    
    case 'Иша':
      return [
        {
          id: `namaz-${prayer.title}-${Date.now()}-1`,
          expanded: false,
          time: '',
          text: 'НАМАЗ',
          isFixed: true,
          status: 'none'
        },
        {
          id: `ratibat-${prayer.title}-${Date.now()}-2`,
          expanded: false,
          time: '',
          text: 'Ратибат',
          isFixed: true,
          status: 'none'
        },
        {
          id: `tahajjud-${prayer.title}-${Date.now()}-3`,
          expanded: false,
          time: '',
          text: 'Тахаджуд',
          isFixed: true,
          status: 'none',
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
          isFixed: true,
          status: 'none'
        }
      ]
  }
}

const currentDate = ref(new Date())
const selectedDate = ref(new Date())

const fixedInputs = ref(prayers.map(prayer => getInitialInputsForPrayer(prayer)))

const tasks = ref<Task[]>([])

const expandedKey = ref<string | null>(null)


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

const MINUTES_IN_DAY = 24 * 60

const prayerStartMinutes = computed(() =>
  prayers.map(p => {
    const [h, m] = p.time.split(':').map(Number)
    return h * 60 + (m || 0)
  })
)

const cardInterval = (cardIndex: number): { start: number; end: number } => {
  const starts = prayerStartMinutes.value
  const start = starts[cardIndex]
  const end = cardIndex + 1 < starts.length ? starts[cardIndex + 1] : starts[0] + MINUTES_IN_DAY
  return { start, end }
}

const parseTimeRange = (value: string): { start: number; end: number } | null => {
  const parts = value.match(/(\d{1,2}):(\d{2})/g)
  if (!parts || parts.length === 0) return null
  const toMin = (s: string): number | null => {
    const [h, m] = s.split(':').map(Number)
    if (h > 23 || m > 59) return null
    return h * 60 + m
  }
  const first = toMin(parts[0])
  if (first === null) return null
  if (parts.length === 1) return { start: first, end: first }
  const second = toMin(parts[1])
  if (second === null) return { start: first, end: first }
  const end = second < first ? second + MINUTES_IN_DAY : second
  return { start: first, end }
}

const rangesOverlap = (aStart: number, aEnd: number, bStart: number, bEnd: number): boolean => {
  if (aStart === aEnd) return aStart >= bStart && aStart < bEnd
  return aStart < bEnd && bStart < aEnd
}

const taskOverlapsCard = (task: Task, cardIndex: number): boolean => {
  if (task.start === null || task.end === null) return false
  const { start: cs, end: ce } = cardInterval(cardIndex)
  for (const shift of [0, MINUTES_IN_DAY, -MINUTES_IN_DAY]) {
    if (rangesOverlap(task.start + shift, task.end + shift, cs, ce)) return true
  }
  return false
}

const tasksForCard = (cardIndex: number): Task[] => {
  return tasks.value.filter(task => {
    if (taskOverlapsCard(task, cardIndex)) return true
    if ((task.start === null || task.end === null) && task.draftCard === cardIndex) return true
    if (expandedKey.value === `${cardIndex}:${task.id}`) return true
    return false
  })
}

const cardRows = (cardIndex: number): CardRow[] => {
  const fixed = fixedInputs.value[cardIndex]
  const rows: CardRow[] = fixed
    .filter(f => !f.isPinned)
    .map(input => ({ kind: 'fixed' as const, input }))
  for (const task of tasksForCard(cardIndex)) {
    rows.push({ kind: 'task' as const, task })
  }
  for (const input of fixed.filter(f => f.isPinned)) {
    rows.push({ kind: 'fixed' as const, input })
  }
  return rows
}

const rowKey = (row: CardRow): string => (row.kind === 'task' ? row.task.id : row.input.id)
const rowText = (row: CardRow): string => (row.kind === 'task' ? row.task.text : row.input.text)
const rowTime = (row: CardRow): string => (row.kind === 'task' ? row.task.time : row.input.time)
const rowStatus = (row: CardRow): TaskStatus => (row.kind === 'task' ? row.task.status : row.input.status)
const statusClass = (row: CardRow): string => {
  const s = rowStatus(row)
  return s === 'done' ? 'task-status-done' : s === 'pending' ? 'task-status-pending' : ''
}
const statusTitle = (row: CardRow): string => {
  const s = rowStatus(row)
  const name = s === 'done' ? 'Выполнено' : s === 'pending' ? 'Просрочено' : 'Без статуса'
  return `${name} (нажмите, чтобы сменить статус)`
}
const cycleStatus = (row: CardRow) => {
  const order: TaskStatus[] = ['none', 'done', 'pending']
  const next = order[(order.indexOf(rowStatus(row)) + 1) % order.length]
  if (row.kind === 'task') row.task.status = next
  else row.input.status = next
}
const isRowExpanded = (cardIndex: number, row: CardRow): boolean =>
  row.kind === 'task' && expandedKey.value === `${cardIndex}:${row.task.id}`

const commitTaskTime = (task: Task) => {
  const parsed = parseTimeRange(task.time)
  if (parsed) {
    task.start = parsed.start
    task.end = parsed.end
    task.draftCard = null
  }
}

const onTaskTextInput = (row: CardRow, event: Event) => {
  if (row.kind !== 'task') return
  row.task.text = (event.target as HTMLTextAreaElement).value
}

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

const handleTimeInput = (row: CardRow, event: Event) => {
  if (row.kind !== 'task') return
  const target = event.target as HTMLInputElement
  row.task.time = formatTimeInput(target.value)
}


const expandAndFocus = (cardIndex: number, row: CardRow, field: 'text' | 'time', event: Event) => {
  if (row.kind !== 'task') return
  const key = `${cardIndex}:${row.task.id}`
  if (expandedKey.value === key) {
    expandedKey.value = null
    return
  }
  expandedKey.value = key
  const container = (event.currentTarget as HTMLElement | null)?.closest('.list-item-container')
  nextTick(() => {
    if (!container) return
    const target =
      field === 'text'
        ? container.querySelector('textarea')
        : container.querySelector('.expandable-block-open input')
    ;(target as HTMLElement | null)?.focus()
  })
}

const expandAndFocusText = (cardIndex: number, row: CardRow, event: Event) =>
  expandAndFocus(cardIndex, row, 'text', event)

const expandAndFocusTime = (cardIndex: number, row: CardRow, event: Event) =>
  expandAndFocus(cardIndex, row, 'time', event)


const addNewInput = (cardIndex: number) => {
  const task: Task = {
    id: `new-${Date.now()}-${Math.random()}`,
    text: '',
    time: '',
    start: null,
    end: null,
    status: 'none',
    draftCard: cardIndex
  }
  tasks.value.push(task)
}


let statusClickTimer: ReturnType<typeof setTimeout> | null = null

const onRowClick = (cardIndex: number, row: CardRow, event: MouseEvent) => {
  if (row.kind === 'fixed') {
    cycleStatus(row)
    return
  }
  if (statusClickTimer) {
    clearTimeout(statusClickTimer)
    statusClickTimer = null
    const target = event.target as HTMLElement | null
    const field = target instanceof HTMLInputElement && target.placeholder === 'Время' ? 'time' : 'text'
    expandAndFocus(cardIndex, row, field, event)
    return
  }
  statusClickTimer = setTimeout(() => {
    statusClickTimer = null
    cycleStatus(row)
  }, 250)
}


const handleExpandedBlur = (cardIndex: number, row: CardRow, event: FocusEvent) => {
  if (row.kind !== 'task') return
  const taskId = row.task.id
  commitTaskTime(row.task)
  const container = (event.target as HTMLElement | null)?.closest('.list-item-container')
  setTimeout(() => {
    const activeElement = document.activeElement
    if (container && (!activeElement || !container.contains(activeElement))) {
      if (expandedKey.value === `${cardIndex}:${taskId}`) {
        expandedKey.value = null
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

.bottom-panel {
  height: 20rem;
  min-height: 20rem;
}
/* Кружок статуса задачи в правом верхнем углу плашки (правки N1, N3) */
.task-status-badge {
  position: absolute;
  top: -6px;
  right: 14px;
  width: 18px;
  height: 18px;
  border-radius: 9999px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 10px;
  font-weight: 800;
  line-height: 1;
  color: #fff;
  border: 2px solid #fff;
  box-shadow: 0 1px 5px rgba(0, 0, 0, 0.3);
  cursor: pointer;
  z-index: 5;
  padding: 0;
  transition: transform 0.15s ease, background-color 0.2s ease;
}
.task-status-badge:hover {
  transform: scale(1.15);
}
.task-status-badge:active {
  transform: scale(0.95);
}
.task-status-done {
  background-color: #22c55e;
}
.task-status-pending {
  background-color: #ef4444;
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

.notes-scroll {
  scrollbar-width: thin;
  scrollbar-color: rgba(188, 162, 255, 0.75) rgba(255, 255, 255, 0.08);
}

.notes-scroll::-webkit-scrollbar {
  width: 6px;
}

.notes-scroll::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.06);
  border-radius: 9999px;
  margin: 4px 0;
}

.notes-scroll::-webkit-scrollbar-thumb {
  background: linear-gradient(180deg, rgba(214, 190, 255, 0.95), rgba(140, 89, 208, 0.95));
  border-radius: 9999px;
  border: 1px solid rgba(255, 255, 255, 0.35);
}

.notes-scroll::-webkit-scrollbar-thumb:hover {
  background: linear-gradient(180deg, rgba(227, 210, 255, 1), rgba(140, 89, 208, 1));
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