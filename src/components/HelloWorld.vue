<template>
  <div class="flex flex-col lg:flex-row gap-8 p-8 bg-gray-100 min-h-screen">
    <div class="w-full lg:w-1/2 bg-white p-6 rounded-lg shadow-md">
      <h2 class="text-2xl font-bold mb-6 text-left">Calendar View</h2>
      
      <div class="grid grid-cols-3 gap-4 mb-6">
        <div v-for="platform in platforms" :key="platform.name" class="flex flex-col">
          <div class="flex items-center mb-2">
            <component :is="platform.icon" class="w-6 h-6 mr-2" />
            <span class="text-sm font-medium">{{ platform.name }}</span>
          </div>
          <select v-model="platform.time" class="border rounded p-2 text-sm">
            <option v-for="time in times" :key="time">{{ time }}</option>
          </select>
        </div>
      </div>

      <div class="flex items-center justify-between mb-4">
        <button class="text-gray-600 hover:text-gray-800" @click="prevMonth">
          <ChevronLeftIcon class="w-6 h-6" />
        </button>
        <h3 class="text-lg font-semibold text-left">{{ currentMonthYear }}</h3>
        <button class="text-gray-600 hover:text-gray-800" @click="nextMonth">
          <ChevronRightIcon class="w-6 h-6" />
        </button>
      </div>

      <div class="grid grid-cols-7 gap-2 text-sm">
        <div v-for="day in daysOfWeek" :key="day" class="text-center font-medium text-gray-500">
          {{ day }}
        </div>
        <div 
          v-for="(date, index) in calendarDays" 
          :key="index" 
          class="text-center p-2 cursor-pointer" 
          :class="{ 
            'bg-black text-white rounded-full': date === selectedDate,
            'text-gray-400': date === null 
          }"
          @click="selectDate(date)"
        >
          {{ date || '' }}
        </div>
      </div>
    </div>

    <div class="w-full lg:w-1/2 bg-white p-6 rounded-lg shadow-md flex flex-col h-[calc(100vh-2rem)]">
      <h2 class="text-2xl font-bold mb-6 text-left">Monthly Content Overview</h2>
      <div class="overflow-y-auto flex-grow pr-2 space-y-6 scrollbar-thin scrollbar-thumb-gray-300 scrollbar-track-gray-100">
        <div v-for="(content, date) in monthlyContent" :key="date">
          <h3 class="text-lg font-semibold mb-2 text-left sticky top-0 bg-white py-2 z-10">{{ date }}</h3>
          <div v-for="item in content" :key="item.title" class="mb-4 border border-gray-200 rounded-lg p-4 hover:shadow-md transition-shadow duration-200">
            <h4 class="font-medium text-lg mb-1 text-left">{{ item.title }}</h4>
            <p class="text-sm text-gray-600 mb-2 text-left">{{ item.description }}</p>
            <div class="flex items-center justify-between text-sm">
              <div class="flex items-center">
                <component :is="item.icon" class="w-4 h-4 mr-2" aria-hidden="true" />
                <span class="font-medium">{{ item.channel }}</span>
              </div>
              <span class="text-gray-500">Posts at: {{ item.time }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { Youtube, TikTok, Instagram, ChevronLeftIcon, ChevronRightIcon } from 'lucide-vue-next'

const now = new Date()
const months = [
  "January", "February", "March", "April", "May", "June",
  "July", "August", "September", "October", "November", "December"
]

const currentDate = ref(new Date(now.getFullYear(), now.getMonth()))
const selectedDate = ref(now.getDate())

const platforms = ref([
  { name: 'YouTube', icon: Youtube, time: '15:00' },
  { name: 'TikTok', icon: TikTok || ChevronRightIcon, time: '12:00' },
  { name: 'Instagram', icon: Instagram, time: '18:00' },
])

const times = ['09:00', '12:00', '15:00', '18:00', '21:00']
const daysOfWeek = ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa']

const currentMonthYear = ref(`${months[currentDate.value.getMonth()]} ${currentDate.value.getFullYear()}`)

function getDaysInMonth(year, month) {
  return new Date(year, month + 1, 0).getDate()
}

function getStartDayOfMonth(year, month) {
  return new Date(year, month, 1).getDay()
}

const calendarDays = computed(() => {
  const year = currentDate.value.getFullYear();
  const month = currentDate.value.getMonth();
  
  const daysInMonth = getDaysInMonth(year, month);
  const startDay = getStartDayOfMonth(year, month);
  
  const days = Array(startDay).fill(null);
  for (let i = 1; i <= daysInMonth; i++) {
    days.push(i);
  }
  return days;
});


function selectDate(date) {
  if (date) {
    selectedDate.value = date
  }
}

function nextMonth() {
  currentDate.value.setMonth(currentDate.value.getMonth() + 1)
  updateCurrentMonthYear()
}

function prevMonth() {
  currentDate.value.setMonth(currentDate.value.getMonth() - 1)
  updateCurrentMonthYear()
}

function updateCurrentMonthYear() {
  currentMonthYear.value = `${months[currentDate.value.getMonth()]} ${currentDate.value.getFullYear()}`
}

const monthlyContent = ref({
  'October 1, 2024': [
    {
      title: "Ancient Egypt's Pyramids",
      description: 'Explore the mysteries of the Great Pyramids of Giza.',
      icon: Youtube,
      channel: 'HistoryChannel',
      time: '3:00 PM',
    },
    {
      title: 'Pharaohs of Egypt',
      description: 'A quick look at famous Egyptian Pharaohs.',
      icon: TikTok || ChevronRightIcon,
      channel: '@historybytes',
      time: '12:00 PM',
    },
  ],
  'October 3, 2024': [
    {
      title: "The Roman Empire's Rise",
      description: 'Discover how Rome became the dominant power in the Mediterranean.',
      icon: Youtube,
      channel: 'TimeTravel101',
      time: '3:00 PM',
    },
  ],
  'October 5, 2024': [
    {
      title: 'Medieval Castle Life',
      description: 'A day in the life of medieval castle inhabitants.',
      icon: Instagram,
      channel: '@historygram',
      time: '6:00 PM',
    },
  ],
})
</script>