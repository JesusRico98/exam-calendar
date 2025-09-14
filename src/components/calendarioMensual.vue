<template>
  <div class="calendar-app">
    <h1>Calendario mensual personalizado</h1>

    <form class="controls" @submit.prevent="generate">
      <label>
        Fecha de inicio:
        <input type="date" v-model="startDateStr" required />
      </label>

      <label>
        Días a mostrar:
        <input type="number" v-model.number="daysToShow" min="1" required />
      </label>

      <button type="submit">Generar calendario</button>
    </form>

    <div v-if="months.length === 0" class="hint">
      Introduce una fecha y número de días, luego presiona Generar.
    </div>

    <div class="months">
      <section v-for="month in months" :key="month.key" class="month-card">
        <header class="month-header">
          <h2>{{ month.title }}</h2>
          <div class="weekdays">
            <div v-for="d in weekdayNames" :key="d" class="weekday">{{ d }}</div>
          </div>
        </header>

        <div class="weeks">
          <div class="week" v-for="(week, wi) in month.weeks" :key="wi">
            <div
              class="day"
              v-for="(dayObj, di) in week"
              :key="di"
              :class="dayClass(dayObj, month)"
            >
              <div class="day-number">{{ dayObj.date.getDate() }}</div>
            </div>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const weekdayNames = ['Lun', 'Mar', 'Mié', 'Jue', 'Vie', 'Sáb', 'Dom']
const monthNames = [
  'Enero',
  'Febrero',
  'Marzo',
  'Abril',
  'Mayo',
  'Junio',
  'Julio',
  'Agosto',
  'Septiembre',
  'Octubre',
  'Noviembre',
  'Diciembre',
]
const holidaysFixed = [
  { day: 1, month: 1 },
  { day: 5, month: 2 },
  { day: 18, month: 3 },
  { day: 1, month: 5 },
  { day: 16, month: 9 },
  { day: 20, month: 11 },
  { day: 25, month: 12 },
]

const startDateStr = ref('')
const daysToShow = ref(15)

const startDate = computed(() => {
  return startDateStr.value ? new Date(startDateStr.value + 'T00:00:00') : null
})
const endDate = computed(() => {
  if (!startDate.value) return null
  const d = new Date(startDate.value)
  d.setDate(d.getDate() + (Number(daysToShow.value) - 1))
  return d
})

const months = ref([])

function generate() {
  months.value = []
  if (!startDate.value || !Number(daysToShow.value) || daysToShow.value < 1) return

  const s = new Date(startDate.value)
  const e = new Date(endDate.value)

  let year = s.getFullYear()
  let month = s.getMonth()

  while (year < e.getFullYear() || (year === e.getFullYear() && month <= e.getMonth())) {
    const monthStart = new Date(year, month, 1)
    const monthEnd = new Date(year, month + 1, 0)

    const firstWeekStart = new Date(monthStart)
    const dow = (monthStart.getDay() + 6) % 7
    firstWeekStart.setDate(monthStart.getDate() - dow)

    const weeks = []
    let cursor = new Date(firstWeekStart)

    while (cursor <= monthEnd || weeks.length === 0 || (cursor.getDay() + 6) % 7 !== 0) {
      const week = []
      for (let i = 0; i < 7; i++) {
        week.push({ date: new Date(cursor) })
        cursor.setDate(cursor.getDate() + 1)
      }
      weeks.push(week)
      if (cursor > monthEnd && cursor.getDay() === 1) break
      if (weeks.length > 10) break
    }

    months.value.push({
      key: `${year}-${month}`,
      year,
      monthIndex: month,
      title: `${monthNames[month]} ${year}`,
      weeks,
    })

    month += 1
    if (month > 11) {
      month = 0
      year += 1
    }
  }
}

function dayClass(dayObj, monthObj) {
  const d = dayObj.date
  const classes = []

  if (d.getMonth() !== monthObj.monthIndex) {
    classes.push('outside-month')
    return classes.join(' ')
  }

  if (startDate.value && d < startDate.value) {
    classes.push('out-of-range')
    return classes.join(' ')
  }
  if (endDate.value && d > endDate.value) {
    classes.push('out-of-range')
    return classes.join(' ')
  }

  const isHoliday = holidaysFixed.some((h) => h.day === d.getDate() && h.month === d.getMonth() + 1)
  if (isHoliday) {
    classes.push('holiday')
    return classes.join(' ')
  }

  const jsDow = d.getDay()
  if (jsDow === 6 || jsDow === 0) {
    classes.push('weekend')
    return classes.join(' ')
  }

  classes.push('workday')
  return classes.join(' ')
}
</script>

<style scoped>
.calendar-app {
  font-family:
    Inter,
    system-ui,
    -apple-system,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial;
  padding: 18px;
  max-width: 1100px;
  margin: 0 auto;
}

.controls {
  display: flex;
  gap: 12px;
  align-items: center;
  margin-bottom: 18px;
  flex-wrap: wrap;
}
.controls label {
  display: flex;
  gap: 6px;
  align-items: center;
}
.controls input[type='date'],
.controls input[type='number'] {
  padding: 6px 8px;
}
.controls button {
  padding: 8px 12px;
  border-radius: 8px;
  cursor: pointer;
}

.months {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 18px;
}
.month-card {
  border-radius: 8px;
  padding: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
  background: #fff;
}
.month-header {
  margin-bottom: 8px;
}
.month-header h2 {
  margin: 0 0 6px 0;
  font-size: 1.1rem;
}
.weekdays {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  font-weight: 600;
}
.weekday {
  padding: 6px 0;
  text-align: center;
  font-size: 0.85rem;
}

.weeks {
  display: flex;
  flex-direction: column;
  gap: 6px;
}
.week {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 6px;
}
.day {
  min-height: 60px;
  border-radius: 6px;
  display: flex;
  align-items: flex-start;
  justify-content: flex-end;
  padding: 6px;
  font-size: 0.9rem;
}
.day-number {
  background: rgba(255, 255, 255, 0.25);
  padding: 4px 6px;
  border-radius: 6px;
}
.workday {
  background: #e8f8ec;
  border: 1px solid rgba(0, 120, 0, 0.08);
}
.weekend {
  background: #fff8db;
  border: 1px solid rgba(180, 140, 0, 0.08);
}
.holiday {
  background: #ffe7c7;
  border: 1px solid rgba(200, 100, 0, 0.09);
}
.outside-month,
.out-of-range {
  background: #efefef;
  color: #909090;
  border: 1px solid rgba(0, 0, 0, 0.04);
}
</style>
