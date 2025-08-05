<script setup>
import { ref, computed } from 'vue'
import SignupBarChart from '../components/SignupBarChart.vue'
import SignupPieChart from '../components/SignupPieChart.vue'

const searchTerm = ref('')
const selectedSource = ref('')
const currentPage = ref(1)
const itemsPerPage = 5

const users = ref([
    { id: 1, name: 'Tiegist Girma', email: 'tiegist@gmail.com', source: 'Website', signupDate: '2025-08-01' },
    { id: 2, name: 'selam belete', email: 'selam@gmail.com', source: 'Instagram', signupDate: '2025-08-01' },
    { id: 3, name: 'abebe tesfa', email: 'abebe@gmail.com', source: 'linkedIn', signupDate: '2025-08-01' },
    { id: 4, name: 'mekdes solomon', email: 'mekdes@gmail.com', source: 'linkedIn', signupDate: '2025-08-04' },
    { id: 5, name: 'hermela tamrat', email: 'hermela@gmail.com', source: 'Instagram', signupDate: '2025-08-05' },
    { id: 6, name: 'henok aschalew', email: 'henok@gmail.com', source: 'Website', signupDate: '2025-08-05' },
    { id: 7, name: 'tihtina adane', email: 'tihtina@gmail.com', source: 'Website', signupDate: '2025-08-05' },
    { id: 8, name: 'veronica kirubel', email: 'veronica@gmail.com', source: 'Instagram', signupDate: '2025-08-05' },
    { id: 9, name: 'hanamariam mola', email: 'hanamariam@gmail.com', source: 'Website', signupDate: '2025-08-05a' },
    { id: 10, name: 'beza alemu', email: 'beza@gmail.com', source: 'twitter', signupDate: '2025-08-10' }
])



const filteredUsers = computed(() => {
    return users.value.filter(user =>
        (user.name.toLowerCase().includes(searchTerm.value.toLowerCase()) ||
            user.email.toLowerCase().includes(searchTerm.value.toLowerCase())) &&
        (selectedSource.value === '' || user.source === selectedSource.value)
    )
})

const startIndex = computed(() => (currentPage.value - 1) * itemsPerPage)
const endIndex = computed(() => startIndex.value + itemsPerPage)

const paginatedUsers = computed(() => {
    return filteredUsers.value.slice(startIndex.value, endIndex.value)
})

// Pagination Buttons
function prevPage() {
    if (currentPage.value > 1) currentPage.value--
}
function nextPage() {
    if (endIndex.value < filteredUsers.value.length) currentPage.value++
}

function deleteUser(id) {
    if (confirm('Are you sure you want to delete this user?')) {
        users.value = users.value.filter(user => user.id !== id)
    }
}
function blockUser(id) {
    alert(`User with ID ${id} has been blocked.`)
}

function exportCSV() {
    const headers = ['Name', 'Email', 'Source', 'Signup Date']
    const rows = users.value.map(user => [
        user.name,
        user.email,
        user.source,
        user.signupDate
    ])
    const csvContent = [headers, ...rows].map(e => e.join(',')).join('\n')
    const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' })
    const url = URL.createObjectURL(blob)
    const link = document.createElement('a')
    link.setAttribute('href', url)
    link.setAttribute('download', 'waiting_list.csv')
    link.click()
}
</script>

<template>
    <div class="flex flex-col md:flex-row min-h-screen bg-gray-100">
        <aside class="w-full md:w-64 bg-[#00b073] text-white">
            <img class="p-3 bg-white text-2xl font-bold " src="/logo-tenamart.svg" alt="">
            <nav class="flex flex-col space-y-4 px-6 mt-6">
                <a href="#" class="hover:text-gray-300 font-semibold">Dashboard</a>
                <a href="#" class="font-semibold bg-[#1a806b] px-3 py-1 rounded pl-6"> <span class="fa-solid fa-hourglass-half mr-2"></span>Waiting List</a>
            </nav>
        </aside>

        <main class="flex-1 p-6">
            <div class="flex justify-between items-center mb-4">
                <h1 class="text-2xl font-bold">Waiting List</h1>
                <div class="flex space-x-2">
                    <input type="text" placeholder="Search..." class=" outline-none border border-gray-300 focus:border-[#66b999] focus:ring-teal-600 focus:border-3 w-64 -ml-20  px-3 py-1 rounded-md" v-model="searchTerm" />
                    <select class="shadow-teal-200 shadow-md active:scale-95 cursor-pointer outline-none px-2 py-1 rounded" v-model="selectedSource">
                        <option value="">All Sources</option>
                        <option value="Website">Website</option>
                        <option value="Instagram">Instagram</option>
                        <option value="Referral">Referral</option>
                    </select>
                    <button class="bg-[#00b073] text-white px-4 py-1 rounded hover:bg-[#00b073] active:scale-95 cursor-pointer" @click="exportCSV">
                        Export CSV
                    </button>
                </div>
            </div>
            <div class="w-full max-w-5xl mx-auto h-64 sm:h-72 md:h-96 flex mt-2 mb-10">
                <div class="mr-40">
                    <SignupBarChart :users="users"/>
                </div>
                <div >
                    <SignupPieChart :users="users"/>
                </div>
            </div>

            <div class="bg-white rounded-lg shadow overflow-auto -mt-24">
                <table class="min-w-full table-auto">
                    <thead>
                        <tr class="bg-gray-100 text-left text-sm font-semibold text-gray-700">
                            <th class="px-6 py-3">#</th>
                            <th class="px-6 py-3">Name</th>
                            <th class="px-6 py-3">Email</th>
                            <th class="px-6 py-3">Source</th>
                            <th class="px-6 py-3">Signup Date</th>
                            <th class="px-6 py-3">Actions</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(user, index) in paginatedUsers" :key="user.id"
                            class="border-t-teal-800 hover:bg-blue-50">
                            <td class="px-6 py-3 text-gray-700">{{ startIndex + index + 1 }}</td>
                            <td class="px-6 py-3 font-medium">{{ user.name }}</td>
                            <td class="px-6 py-3">{{ user.email }}</td>
                            <td class="px-6 py-3">{{ user.source }}</td>
                            <td class="px-6 py-3">{{ user.signupDate }}</td>
                            <td class="px-6 py-3 space-x-2">
                                <button class="text-sm text-red-600 hover:underline" @click="deleteUser(user.id)">
                                    Delete <span class="text-gray-400">|</span>
                                </button>
                                <button class="text-sm text-yellow-500 hover:underline" @click="blockUser(user.id)">
                                    Block
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="flex justify-between items-center mt-2">
                <p class="text-sm text-gray-600">
                    Showing {{ startIndex + 1 }} - {{ Math.min(endIndex, filteredUsers.length) }} of
                    {{ filteredUsers.length }}
                </p>
                <div class="flex space-x-1">
                    <button class="px-3 py-1 rounded bg-gray-200" @click="prevPage" :disabled="currentPage === 1">
                        Prev
                    </button>
                    <button class="px-3 py-1 rounded bg-gray-200" @click="nextPage"
                        :disabled="endIndex >= filteredUsers.length">
                        Next
                    </button>
                </div>
            </div>
        </main>
    </div>
</template>
