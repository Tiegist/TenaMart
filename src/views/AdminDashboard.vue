<script setup>
import { ref, computed } from 'vue'
import SignupBarChart from '../components/SignupBarChart.vue'
import SignupPieChart from '../components/SignupPieChart.vue'
import SideBar from '../components/SideBar.vue'

const searchTerm = ref('')
const selectedSource = ref('')
const currentPage = ref(1)
const itemsPerPage = 5
const blockdUSer = ref({})


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
    alert(`User with ID ${id} has been ${blockdUSer.value[id] ? 'unblocked' : 'blocked'}.`)
    blockdUSer.value[id] = !blockdUSer.value[id]
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
    <div class="lg:flex lg:flex-col md:flex-row min-h-screen bg-gray-100">
        <SideBar />

        <main class="lg:flex-1 p-6 pt-4 lg:ml-64">
            <div class="fixed top-0 lg:left-68 md:left-20 left-0 right-4 p-2 bg-gray-100">
                <div
                    class="flex flex-col md:flex-row justify-between items-start md:items-center space-y-2 md:space-y-0 md:space-x-4">

                    <h1 class="text-2xl font-semibold">Waiting List</h1>

                    <div
                        class="flex flex-col sm:flex-row flex-wrap items-start sm:items-center space-y-2 sm:space-y-0 sm:space-x-2 w-full md:w-auto">

                        <input type="text" placeholder="Search..."
                            class="outline-none border border-gray-300 transition-all duration-100 focus:border-[#66b999] focus:ring-teal-600 focus:border-3 w-full sm:w-64 px-3 py-1 rounded-md"
                            v-model="searchTerm" />

                        <select v-model="selectedSource"
                            class="px-4 py-2 w-full sm:w-auto rounded-lg border border-gray-300 shadow-md focus:outline-none bg-white text-gray-700 font-medium cursor-pointer">
                            <option value="" class="text-gray-800 cursor-pointer">All Sources</option>
                            <option value="Website" class="text-gray-800 cursor-pointer">Website</option>
                            <option value="Instagram" class="text-gray-800 cursor-pointer">Instagram</option>
                            <option value="linkedIn" class="text-gray-800 cursor-pointer">LinkedIn</option>
                            <option value="twitter" class="text-gray-800 cursor-pointer">Twitter</option>
                        </select>

                        <button
                            class="bg-[#00b073] text-white px-4 py-1 rounded hover:bg-[#00b073] active:scale-95 cursor-pointer w-full sm:w-auto"
                            @click="exportCSV">
                            Export CSV
                        </button>

                    </div>
                </div>
            </div>

            <div class="p-4  shadow-gray-300 shadow-lg mt-4 ">
                <div
                    class="lg:w-full md:w-full max-w-5xl mx-auto h-64 sm:h-72 md:h-96 lg:flex mb-120 mt-32 md:mt-10 md:mb-78 lg:mt-2 lg:mb-10">
                    <div class="lg:mr-40 md:mr-40 mr-10 w-4/5 mx-auto ">
                        <SignupBarChart :users="users" />
                    </div>
                    <div>
                        <SignupPieChart :users="users" />
                    </div>
                </div>

                <div class="bg-white rounded-lg shadow overflow-auto lg:-mt-24 overflow-x-auto">
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
                                class="border-t-teal-800 hover:bg-blue-50 shadow-sm">
                                <td class="px-6 py-3 text-gray-700">{{ startIndex + index + 1 }}</td>
                                <td class="px-6 py-3 ">{{ user.name }}</td>
                                <td class="px-6 py-3">{{ user.email }}</td>
                                <td class="px-6 py-3">{{ user.source }}</td>
                                <td class="px-6 py-3">{{ user.signupDate }}</td>
                                <td class="px-6 py-3 space-x-2">
                                    <button class="text-sm text-red-600 cursor-pointer hover:underline"
                                        @click="deleteUser(user.id)">
                                        Delete <span class="text-gray-400">|</span>
                                    </button>
                                    <button class="text-sm text-yellow-500 cursor-pointer hover:underline"
                                        @click="blockUser(user.id)">
                                        {{ blockdUSer[user.id] ? 'Unblock' : 'Block' }}
                                    </button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <div class="flex flex-wrap justify-center items-center mt-6 space-x-2">
                    <button @click="prevPage" :disabled="currentPage === 1" :class="[
                        'px-4 py-2 rounded-lg text-sm font-medium transition-all duration-200',
                        currentPage === 1
                            ? 'cursor-not-allowed text-gray-500 border border-gray-600'
                            : 'cursor-pointer hover:bg-[#00b073] hover:text-white text-gray-300 border border-gray-600'
                    ]">
                        Prev
                    </button>

                    <button @click="nextPage" :disabled="endIndex >= filteredUsers.length" :class="[
                        'px-4 py-2 rounded-lg text-sm font-medium transition-all duration-200',
                        endIndex >= filteredUsers.length
                            ? 'cursor-not-allowed text-gray-500 border border-gray-600'
                            : 'cursor-pointer hover:bg-[#00b073] hover:text-white text-gray-300 border border-gray-600'
                    ]">
                        Next
                    </button>
                </div>

                <div class="flex justify-center items-center mt-2">
                    <p class="text-sm text-gray-400">
                        Showing {{ startIndex + 1 }} - {{ Math.min(endIndex, filteredUsers.length) }}
                        of {{ filteredUsers.length }}
                    </p>
                </div>


            </div>
        </main>
    </div>
</template>
