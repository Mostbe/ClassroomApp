<template>
  <div class="min-h-screen bg-gray-50 p-4 sm:p-6">
    <div class="max-w-screen-2xl mx-auto">
      <h1 class="text-3xl sm:text-4xl font-bold text-gray-900 mb-6">Digitales Notenbuch</h1>

      <div class="bg-white p-4 sm:p-6 rounded-2xl shadow-sm mb-6">
        <div class="grid gap-4 lg:grid-cols-[1.5fr_1fr]">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Notenbuch auswählen</label>
            <div class="flex flex-wrap gap-2 items-center">
              <div class="flex flex-wrap gap-2">
                <button
                  v-for="gradebook in gradebooks"
                  :key="gradebook.id"
                  @click="selectGradebook(gradebook.id)"
                  :class="gradebook.id === activeGradebookId ? 'bg-slate-900 text-white' : 'bg-slate-100 text-slate-700 hover:bg-slate-200'"
                  class="rounded-full px-4 py-2 text-sm font-medium transition"
                >
                  {{ gradebook.name }}
                </button>
              </div>
              <div class="ml-auto flex gap-2 flex-wrap items-center">
                <button
                  type="button"
                  @click="shareData"
                  class="rounded-xl bg-purple-600 px-4 py-2 text-sm font-semibold text-white transition hover:bg-purple-700"
                  title="Backup teilen / speichern (Web Share API)"
                >
                  🔗 Share
                </button>
                <button
                  type="button"
                  @click="exportData"
                  class="rounded-xl bg-slate-800 px-4 py-2 text-sm font-semibold text-white transition hover:bg-slate-900"
                >
                  💾 Export
                </button>
                <label class="inline-flex cursor-pointer rounded-xl bg-slate-100 px-4 py-2 text-sm font-semibold text-slate-700 transition hover:bg-slate-200">
                  📂 Import
                  <input type="file" accept="application/json" @change="importData" class="hidden" />
                </label>
              </div>
            </div>
          </div>

          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Neues Notenbuch anlegen</label>
            <div class="flex flex-col sm:flex-row gap-2">
              <input
                v-model="newClassName"
                type="text"
                placeholder="Klasse z. B. 7A"
                class="w-full rounded-xl border border-gray-300 px-4 py-3 text-sm shadow-sm focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-100"
              />
              <button
                type="button"
                @click="addGradebook"
                class="inline-flex items-center justify-center rounded-xl bg-green-600 px-4 py-3 text-sm font-semibold text-white transition hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-green-400"
              >
                Anlegen
              </button>
            </div>
            <p class="mt-3 text-sm text-gray-500">Erstelle unterschiedliche Gradebooks für verschiedene Klassen.</p>
          </div>
        </div>
      </div>

      <div v-if="lastSaved" class="text-xs text-gray-500 mb-4 text-center">
        💾 Zuletzt gespeichert: {{ lastSaved }}
      </div>
      <div v-if="!storageAvailable" class="text-xs text-red-600 mb-4 text-center">
        ⚠️ Lokales Speichern ist nicht verfügbar. Verwende einen normalen Safari-Tab und nicht den privaten Modus.
      </div>

      <div v-if="activeGradebook" class="bg-white p-4 sm:p-6 rounded-2xl shadow-sm mb-6">
        <div class="grid gap-4 lg:grid-cols-[1.8fr_1.2fr]">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Schüler hinzufügen</label>
            <div class="flex flex-col sm:flex-row gap-2">
              <input
                v-model="newStudentName"
                @keyup.enter="addStudent"
                type="text"
                placeholder="Name eingeben..."
                class="w-full rounded-xl border border-gray-300 px-4 py-3 text-sm shadow-sm focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-100"
              />
              <button
                type="button"
                @click="addStudent"
                class="inline-flex items-center justify-center rounded-xl bg-blue-600 px-4 py-3 text-sm font-semibold text-white transition hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-400"
              >
                + Schüler hinzufügen
              </button>
            </div>
          </div>

          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Neue Stunden-Spalte</label>
            <div class="flex flex-col sm:flex-row gap-2">
              <input
                type="date"
                v-model="newHourDate"
                class="w-full rounded-xl border border-gray-300 px-4 py-3 text-sm shadow-sm focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-100"
              />
              <button
                type="button"
                @click="addHourColumn"
                class="inline-flex items-center justify-center rounded-xl bg-indigo-600 px-4 py-3 text-sm font-semibold text-white transition hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-400"
              >
                + Spalte hinzufügen
              </button>
            </div>
            <p class="mt-3 text-sm text-gray-500">Datum der neuen Stunde wird gespeichert.</p>
          </div>
        </div>
      </div>

      <div v-if="activeGradebook" class="bg-white rounded-2xl shadow-sm overflow-x-auto touch-pan-x">
        <table @keydown.enter.prevent="focusNextInput" class="min-w-[1200px] w-full border-separate border-spacing-0 text-sm">
          <thead>
            <tr class="bg-slate-800 text-white">
              <th class="sticky left-0 z-20 bg-slate-800 px-4 py-3 border-r border-slate-700 text-left font-semibold">Schüler</th>
              <th
                :colspan="activeGradebook?.hourColumns.length ?? 0"
                class="px-4 py-3 text-center border-r border-slate-700 font-semibold"
              >
                Stundennoten
              </th>
              <th colspan="6" class="px-4 py-3 text-center border-r border-slate-700 font-semibold">Klausuren</th>
              <th colspan="4" class="px-4 py-3 text-center border-r border-slate-700 font-semibold">Quartalsnoten</th>
              <th class="px-4 py-3 text-center border-r border-slate-700 font-semibold">Ø Mündlich</th>
              <th class="px-4 py-3 text-center font-semibold">Endnote</th>
            </tr>
            <tr class="bg-slate-100 text-slate-700">
              <th class="sticky left-0 z-20 bg-slate-100 px-4 py-3 border-r border-slate-200"></th>
              <th
                v-for="(column, index) in activeGradebook.hourColumns"
                :key="column.id"
                class="px-2 py-3 text-center border-r border-slate-200 min-w-[96px]"
              >
                <div class="text-xs font-semibold text-slate-800">{{ formatDateHeader(column.date) }}</div>
                <div class="text-[11px] text-slate-500">Std {{ index + 1 }}</div>
              </th>
              <th v-for="i in 6" :key="`exam-${i}`" class="px-2 py-3 text-center border-r border-slate-200 min-w-[80px] bg-violet-50 text-violet-700">
                K{{ i }}
              </th>
              <th v-for="i in 4" :key="`quarter-${i}`" class="px-2 py-3 text-center border-r border-slate-200 min-w-[80px] bg-emerald-50 text-emerald-700">
                Q{{ i }}
              </th>
              <th class="px-2 py-3 text-center border-r border-slate-200 min-w-[90px] bg-orange-50 text-orange-700">Ø</th>
              <th class="px-2 py-3 text-center min-w-[90px] bg-rose-50 text-rose-700">Ende</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(student, index) in activeGradebook.students" :key="student.id" class="border-b border-slate-200 hover:bg-slate-50">
              <td class="sticky left-0 z-10 bg-white px-4 py-3 border-r border-slate-200 min-w-[180px]">
                <div class="flex items-center justify-between gap-3">
                  <span class="font-medium text-slate-900">{{ student.name }}</span>
                  <button
                    type="button"
                    @click="removeStudent(index)"
                    class="rounded-lg bg-red-100 px-3 py-1 text-xs font-semibold text-red-700 transition hover:bg-red-200"
                  >
                    Entfernen
                  </button>
                </div>
              </td>
              <td
                v-for="(column, colIndex) in activeGradebook.hourColumns"
                :key="`${student.id}-hour-${column.id}`"
                class="px-2 py-3 text-center border-r border-slate-200 bg-slate-50"
              >
                <input
                  v-model="student.hours[colIndex]"
                  @input="student.hours[colIndex] = normalizeGradeEvent($event)"
                  @change="saveToStorage"
                  type="text"
                  maxlength="3"
                  placeholder="++ / + / 0 / - / --"
                  class="mx-auto w-16 rounded-lg border border-gray-300 px-2 py-1 text-center text-sm shadow-sm focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-100"
                />
              </td>
              <td v-for="i in 6" :key="`${student.id}-exam-${i}`" class="px-2 py-3 text-center border-r border-slate-200 bg-violet-50">
                <input
                  v-model="student.exams[i - 1]"
                  @input="student.exams[i - 1] = normalizeGradeEvent($event)"
                  @change="saveToStorage"
                  type="text"
                  maxlength="3"
                  placeholder="1-6 / ++ / + / 0 / - / --"
                  class="mx-auto w-16 rounded-lg border border-gray-300 px-2 py-1 text-center text-sm shadow-sm focus:border-violet-500 focus:outline-none focus:ring-2 focus:ring-violet-100"
                />
              </td>
              <td v-for="i in 4" :key="`${student.id}-quarter-${i}`" class="px-2 py-3 text-center border-r border-slate-200 bg-emerald-50">
                <input
                  v-model="student.quarters[i - 1]"
                  @input="student.quarters[i - 1] = normalizeGradeEvent($event)"
                  @change="saveToStorage"
                  type="text"
                  maxlength="3"
                  placeholder="1-6 / ++ / + / 0 / - / --"
                  class="mx-auto w-16 rounded-lg border border-gray-300 px-2 py-1 text-center text-sm shadow-sm focus:border-emerald-500 focus:outline-none focus:ring-2 focus:ring-emerald-100"
                />
              </td>
              <td class="px-2 py-3 text-center border-r border-slate-200 bg-orange-50">
                <div class="text-[11px] text-slate-600">Empfehlung</div>
                <div class="mb-2 text-lg font-semibold text-slate-900">{{ calculateRecommendationSymbol(student) }}</div>
                <input
                  v-model="student.oralManual"
                  @input="student.oralManual = normalizeGradeEvent($event)"
                  @change="saveToStorage"
                  type="text"
                  maxlength="3"
                  placeholder="++ / + / 0 / - / --"
                  class="mx-auto w-16 rounded-lg border border-gray-300 px-2 py-1 text-center text-sm shadow-sm focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-100"
                />
              </td>
              <td class="px-2 py-3 text-center bg-rose-50">
                <input
                  v-model="student.finalManual"
                  @input="student.finalManual = normalizeGradeEvent($event)"
                  @change="saveToStorage"
                  type="text"
                  maxlength="3"
                  placeholder="++ / + / 0 / - / --"
                  class="mx-auto w-16 rounded-lg border border-gray-300 px-2 py-1 text-center text-sm shadow-sm focus:border-rose-500 focus:outline-none focus:ring-2 focus:ring-rose-100"
                />
                <div v-if="!student.finalManual" class="mt-2 text-[11px] text-slate-600">
                  auto: {{ calculateFinalGrade(student) }}
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="mt-6 rounded-2xl border border-slate-200 bg-slate-50 p-4 text-sm text-slate-700">
        <p><strong>Hinweis:</strong> Für Stunden-Noten kannst du ++ / + / 0 / - / -- verwenden. Das System berechnet daraus eine empfohlene mündliche Note. Du kannst die tatsächliche mündliche Note und die Endnote aber manuell überschreiben.</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, reactive, ref, toRaw, watch } from 'vue'

interface HourColumn {
  id: number
  date: string
}

interface Student {
  id: number
  name: string
  hours: string[]
  exams: string[]
  quarters: string[]
  oralManual: string
  finalManual: string
}

interface Gradebook {
  id: number
  name: string
  students: Student[]
  hourColumns: HourColumn[]
}

const gradebooks = reactive<Gradebook[]>([])
const activeGradebookId = ref<number | null>(null)
const newClassName = ref('7A')
const newStudentName = ref('')
const newHourDate = ref(formatDate(new Date()))
const lastSaved = ref<string>('')
const storageAvailable = ref(true)
let nextStudentId = 1
let nextHourColumnId = 1
let nextGradebookId = 1

const activeGradebook = computed(() => gradebooks.find(book => book.id === activeGradebookId.value) ?? null)

const STORAGE_KEY = 'gradebook-data'

function loadSavedData() {
  const saved = localStorage.getItem(STORAGE_KEY)
  if (!saved) return
  try {
    const parsed = JSON.parse(saved) as {
      gradebooks: Gradebook[]
      nextStudentId: number
      nextHourColumnId: number
      nextGradebookId: number
      activeGradebookId: number | null
    }
    if (Array.isArray(parsed.gradebooks)) {
      gradebooks.splice(0, gradebooks.length, ...parsed.gradebooks)
      nextStudentId = parsed.nextStudentId ?? nextStudentId
      nextHourColumnId = parsed.nextHourColumnId ?? nextHourColumnId
      nextGradebookId = parsed.nextGradebookId ?? nextGradebookId
      activeGradebookId.value = parsed.activeGradebookId ?? activeGradebookId.value
    }
  } catch {
    console.warn('Ungültige gespeicherte Notenbuchdaten.')
  }
}

function saveToStorage() {
  const payload = {
    gradebooks: toRaw(gradebooks),
    nextStudentId,
    nextHourColumnId,
    nextGradebookId,
    activeGradebookId: activeGradebookId.value
  }
  try {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(payload))
    storageAvailable.value = true
    const now = new Date()
    lastSaved.value = now.toLocaleTimeString('de-DE', { hour: '2-digit', minute: '2-digit', second: '2-digit' })
  } catch (error) {
    storageAvailable.value = false
    console.warn('Speichern nicht möglich:', error)
  }
}

function exportData() {
  const payload = JSON.stringify({ gradebooks: toRaw(gradebooks) }, null, 2)
  const blob = new Blob([payload], { type: 'application/json' })
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.href = url
  link.download = 'gradebook-backup.json'
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)
  URL.revokeObjectURL(url)
}

function shareData() {
  const payload = JSON.stringify({ gradebooks: toRaw(gradebooks) }, null, 2)
  const blob = new Blob([payload], { type: 'application/json' })
  const timestamp = new Date().toISOString().split('T')[0]
  const filename = `gradebook-backup-${timestamp}.json`
  
  if (navigator.share) {
    navigator.share({
      title: 'Notenbuch Backup',
      text: 'Sicherung aller Notenbücher',
      files: [new File([blob], filename, { type: 'application/json' })]
    }).catch(error => {
      if (error.name !== 'AbortError') {
        console.error('Share fehlgeschlagen:', error)
        exportData()
      }
    })
  } else {
    exportData()
  }
}

function importData(event: Event) {
  const file = (event.target as HTMLInputElement)?.files?.[0]
  if (!file) return

  const reader = new FileReader()
  reader.onload = () => {
    try {
      const imported = JSON.parse(reader.result as string) as { gradebooks: Gradebook[] }
      if (!Array.isArray(imported.gradebooks)) throw new Error('Invalid format')
      gradebooks.splice(0, gradebooks.length, ...imported.gradebooks)
      activeGradebookId.value = gradebooks[0]?.id ?? null
      nextStudentId = Math.max(1, ...gradebooks.flatMap(book => book.students.map(student => student.id)), 0) + 1
      nextHourColumnId = Math.max(1, ...gradebooks.flatMap(book => book.hourColumns.map(column => column.id)), 0) + 1
      nextGradebookId = Math.max(1, ...gradebooks.map(book => book.id), 0) + 1
      saveToStorage()
    } catch (error) {
      console.error('Import fehlgeschlagen:', error)
      alert('Import fehlgeschlagen. Bitte stellen Sie sicher, dass die Datei das richtige Format hat.')
    }
  }
  reader.readAsText(file)
}

function formatDate(date: Date | string): string {
  const parsed = typeof date === 'string' ? new Date(date) : date
  return parsed.toISOString().slice(0, 10)
}

function focusNextInput(event: KeyboardEvent) {
  const target = event.target as HTMLElement | null
  if (!(target instanceof HTMLInputElement)) return
  const inputs = Array.from(document.querySelectorAll<HTMLInputElement>('table input'))
  const currentIndex = inputs.indexOf(target)
  if (currentIndex === -1) return
  const nextInput = inputs[currentIndex + 1]
  if (nextInput) nextInput.focus()
}

function formatDateHeader(date: string): string {
  const parsed = new Date(date)
  if (Number.isNaN(parsed.getTime())) return '-'
  return parsed.toLocaleDateString('de-DE', { day: '2-digit', month: '2-digit' })
}

function createGradebook(name: string): Gradebook {
  return {
    id: nextGradebookId++,
    name,
    students: [],
    hourColumns: []
  }
}

function addGradebook() {
  const name = newClassName.value.trim() || `Klasse ${nextGradebookId}`
  const newBook = createGradebook(name)
  addInitialHourColumns(newBook, 5)
  gradebooks.push(newBook)
  activeGradebookId.value = newBook.id
  newClassName.value = ''
  saveToStorage()
}

function selectGradebook(id: number) {
  activeGradebookId.value = id
}

function addStudent() {
  if (!activeGradebook.value) return

  const name = newStudentName.value.trim()
  if (!name) return

  const newStudent: Student = {
    id: nextStudentId++,
    name,
    hours: Array(activeGradebook.value.hourColumns.length).fill(''),
    exams: Array(6).fill(''),
    quarters: Array(4).fill(''),
    oralManual: '',
    finalManual: ''
  }

  activeGradebook.value.students.push(newStudent)
  newStudentName.value = ''
  saveToStorage()
}

function removeStudent(index: number) {
  if (!activeGradebook.value) return
  activeGradebook.value.students.splice(index, 1)
  saveToStorage()
}

function ensureHours(student: Student) {
  if (!activeGradebook.value) return
  while (student.hours.length < activeGradebook.value.hourColumns.length) {
    student.hours.push('')
  }
}

function addHourColumn() {
  if (!activeGradebook.value || !newHourDate.value) return

  activeGradebook.value.hourColumns.push({ id: nextHourColumnId++, date: formatDate(newHourDate.value) })
  activeGradebook.value.students.forEach(ensureHours)

  const next = new Date(newHourDate.value)
  next.setDate(next.getDate() + 1)
  newHourDate.value = formatDate(next)
  saveToStorage()
}

function normalizeGradeInput(value: string): string {
  const normalized = value.trim().replace(',', '.')
  if (!normalized) return ''

  const allowedSymbols = ['++', '+', '0', '-', '--']
  if (allowedSymbols.includes(normalized)) return normalized
  if (/^[1-6]$/.test(normalized)) return normalized
  return ''
}

function normalizeGradeEvent(event: Event): string {
  const target = event.target as HTMLInputElement | null
  return normalizeGradeInput(target?.value ?? '')
}

function mapSymbolToValue(symbol: string): number | null {
  switch (symbol.trim()) {
    case '++':
      return 1.0
    case '+':
      return 2.0
    case '0':
      return 3.0
    case '-':
      return 4.0
    case '--':
      return 5.5
    default:
      return null
  }
}

function mapValueToSymbol(value: number): string {
  if (value <= 1.5) return '++'
  if (value <= 2.5) return '+'
  if (value <= 3.5) return '0'
  if (value <= 4.5) return '-'
  return '--'
}

function parseGrade(value: string): number | null {
  const str = value?.trim().toLowerCase()
  if (!str) return null

  const symbolValue = mapSymbolToValue(str)
  if (symbolValue !== null) return symbolValue
  if (/^[1-6]$/.test(str)) return Number(str)

  return null
}

function calculateAverage(grades: string[]): string {
  const numbers = grades
    .map(parseGrade)
    .filter((grade): grade is number => grade !== null)

  if (!numbers.length) return '-'
  const avg = numbers.reduce((sum, value) => sum + value, 0) / numbers.length
  return avg.toFixed(2)
}

function calculateRecommendationSymbol(student: Student): string {
  const average = calculateAverage(student.hours)
  if (average === '-') return '-'
  return mapValueToSymbol(parseFloat(average))
}

function calculateFinalGrade(student: Student): string {
  const manualValue = parseGrade(student.finalManual)
  if (manualValue !== null) {
    return student.finalManual.trim()
  }

  const oralValue = parseGrade(student.oralManual) ?? mapSymbolToValue(calculateRecommendationSymbol(student))
  if (oralValue === null) return '-'

  const examAvgValues = student.exams
    .map(parseGrade)
    .filter((grade): grade is number => grade !== null)

  if (!examAvgValues.length) return '-'

  const examAvg = examAvgValues.reduce((sum, value) => sum + value, 0) / examAvgValues.length
  return ((oralValue + examAvg) / 2).toFixed(2)
}

function addInitialHourColumns(book: Gradebook, count: number) {
  const start = new Date()
  for (let i = 0; i < count; i++) {
    const next = new Date(start)
    next.setDate(start.getDate() + i)
    book.hourColumns.push({ id: nextHourColumnId++, date: formatDate(next) })
  }
}

onMounted(() => {
  loadSavedData()
  if (!gradebooks.length) {
    const defaultBook = createGradebook('Klasse 1A')
    addInitialHourColumns(defaultBook, 5)
    gradebooks.push(defaultBook)
    activeGradebookId.value = defaultBook.id
  }
})

watch(gradebooks, saveToStorage, { deep: true, flush: 'sync' })
watch(activeGradebookId, saveToStorage)
</script>

<style scoped>
input[type="text"] {
  font-family: inherit;
}
</style>
