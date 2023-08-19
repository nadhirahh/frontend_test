<script setup>
import { ref, watch, computed, onMounted } from 'vue'
import { Icon } from '@iconify/vue';
const headers = ref(["#", "STATUS", "TITLE", "ACTION"])


const tooltipIcon = ref(null);
const currentTaskId = ref(null)
const showModal = ref(false)

onMounted(() => {
    const tooltipTriggerList = [].slice.call(
        document.querySelectorAll('[data-bs-toggle="tooltip"]')
    );
    tooltipTriggerList.forEach(function (tooltipTriggerEl) {
        new bootstrap.Tooltip(tooltipTriggerEl)
    });
});

var data = ref([
    { id: 'task 1', task_title: 'Learn HTML', duedate: "14/08/2023", status: "completed" },
    { id: 'task 2', task_title: 'Learn JavaScript', duedate: "15/08/2023", status: "not completed" },
    { id: 'task 3', task_title: 'Learn Vue', duedate: "16/08/2023", status: "completed" }
])
const props = defineProps({
    taskData: Object
});

// Watching the prop directly
watch(() => props.taskData, (newVal, oldVal) => {
    if (newVal && newVal !== oldVal) {
        addNewTask();
    }
}, { deep: true });

function openModal(id) {
      showModal.value  = true;
      currentTaskId.value = id
    }
    function  closeModal() {
      showModal.value  = false;
      currentTaskId.value = null
    }

function addNewTask() {
    const latestTaskCount = data.value.length;
    const newTask = {
        id: `task ${latestTaskCount + 1}`,
        task_title: props.taskData.task_title,
        duedate: props.taskData.duedate,
        status: "not completed"
    };
    data.value.push(newTask);
}

function removeTask() {
    data.value = data.value.filter(item => item.id !== currentTaskId.value)
    closeModal()
}

function markCompleted(id) {
    data.value = data.value.map(item => ({
        ...item,
        status: item.id == id ? 'completed' : item.status
    }))
}

const currentPage = ref(1);
const itemsPerPage = ref(5);
const totalPages = computed(() => Math.ceil(data.value.length / itemsPerPage.value));

function nextPage() {
    if (currentPage.value < totalPages.value) {
        currentPage.value++;
    }
}

function prevPage() {
    if (currentPage.value > 1) {
        currentPage.value--;
    }
}

function confirmationModal(taskId) {
    const modal = new bootstrap.Modal(document.getElementById('confirmationModal'));
    modal.show();
    currentTaskId.value = taskId
}

const paginatedData = computed(() => {
    const start = (currentPage.value - 1) * itemsPerPage.value;
    const end = start + itemsPerPage.value;
    return data.value.slice(start, end);
});


</script>

<template>
    <div>
        <div>
            <table class="table custom-table">
                <thead>
                    <tr>
                        <th class="" :style="idx == 2 ? 'padding-left: 20px;' : ''"
                            :class="idx == 2 ? 'd-flex justify-content-start' : ''" scope="col"
                            v-for="(header, idx) in headers" :key="header">{{ header }}</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(row, idx) in paginatedData" :key="row.id" class="row-table">
                        <th>{{ idx + 1 }}</th>
                        <th>
                            <Icon v-if="row.status == 'completed'" icon="fluent-mdl2:completed" color="green" width="25"
                                data-bs-toggle="tooltip" data-bs-placement="left" title="Task Completed"
                                ref="tooltipIcon" />
                            <Icon v-else icon="fluent-mdl2:error" color="orange" width="25" class="cursor-style"
                                @click="markCompleted(row.id)" data-bs-toggle="tooltip" data-bs-placement="left"
                                title="Mark as completed" ref="tooltipIcon" />
                        </th>
                        <td style="padding-left: 20px;">
                            <div class="table-task">
                                {{ row.task_title }}
                            </div>
                            <div class="date-style">
                                {{ row.duedate }}
                            </div>
                        </td>
                        <th>
                            <div class="d-flex justify-content-center">
                            <div class="delete-button d-flex justify-content-center align-items-center cursor-style"
                                data-bs-toggle="tooltip" data-bs-placement="right" @click="openModal(row.id)">
                                <Icon icon="ph:trash-bold" width="25" title="Remove Task"
                                    ref="tooltipIcon" />
                            </div>
                        </div>
                        </th>
                    </tr>
                </tbody>
            </table>
            <div v-if="data.length > itemsPerPage" class="pagination">
                <div class="cursor-style" @click="prevPage" :disabled="currentPage === 1">
                    <Icon icon="grommet-icons:previous" width="15" />
                </div>
                Showing {{ itemsPerPage }} of {{ data.length }}
                <div class="cursor-style" @click="nextPage" :disabled="currentPage === totalPages">
                    <Icon icon="grommet-icons:next" width="15" />
                </div>
            </div>

        </div>
        <transition name="fade">
            <div v-if="showModal" class="modal-backdrop">
                <!-- Modal -->
                <div ref="confirmation-modal" class="modal-confirmation">
                    <div class="d-flex justify-content-between">
                        <div class="modal-title">
                            <Icon icon="fluent-mdl2:error" color="red" width="25" />
                            <span class="modal-header">Are you sure?</span>
                        </div>
                        <!-- Bind closeModal to the close icon -->
                        <Icon icon="iconamoon:close-thin" width="20"  class="cursor-style" @click="closeModal" />
                    </div>
                    <div class="modal-confirmation-content">
                        Your action is will not be reversable
                    </div>
                    <div class="button-modal mt-3">
                        <!-- Bind closeModal to the cancel button -->
                        <button class="cancel-button" @click="closeModal">Cancel</button>
                        <button class="confirm-button" @click="removeTask">I'm sure!</button>
                    </div>
                </div>
            </div>
        </transition>

    </div>
</template>