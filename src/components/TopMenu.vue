<script setup>
import { Icon } from '@iconify/vue';
import { ref, onMounted } from 'vue'
import Vue3Datepicker from '@vuepic/vue-datepicker'
import DatePickerInstance from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'
var task_title = ref('')
var duedate = ref('')
const datepickerRef = ref(null);
var datepicker = ref<DatePickerInstance>(null);

const emit = defineEmits(['taskData'])

const tooltipIcon = ref(null);

onMounted(() => {
    const tooltipTriggerList = [].slice.call(
        document.querySelectorAll('[data-bs-toggle="tooltip"]')
    );
    tooltipTriggerList.forEach(function (tooltipTriggerEl) {
        new bootstrap.Tooltip(tooltipTriggerEl)
    });
});

function formatDate(date) {
    const day = String(date.getDate()).padStart(2, '0');
    const month = String(date.getMonth() + 1).padStart(2, '0'); // January is 0!
    const year = date.getFullYear();
    console.log('aaa iii')
    duedate.value = `${day}/${month}/${year}`;
}

function showDatepicker() {
    console.log('aaa',  datepicker)
    // datepicker.openMenu()
    duedate.value = 'dd/mm/yyyy'
      if (datepicker && datepicker.openMenu()) {
        datepicker.openMenu();
      }
    }

function sendData() {
    if (task_title.value != '' && duedate.value != '') {
        const taskData = {
        task_title: task_title.value,
        duedate: duedate.value,
    };
    emit('taskData', taskData)

    // reset input after add task
    task_title.value = null
    duedate.value =null
    document.getElementById('title-label').classList.remove('active')
    document.getElementById('duedate-label').classList.remove('active')
    } else {
        alert('Task title and duedate cannot be empty')
    }
  
}


</script>

<template>
    <div>
        <div class="menu-style">
            <!-- <div class="custom-input"> -->
                <input id="title" type="text" class="text-color custom-input" v-model="task_title" placeholder="Task Title">
            <!-- </div> -->
            <div class="input-field">
                <input id="duedate" type="text" class="text-color custom-input" v-model="duedate"  placeholder="Duedate" @click="showDatepicker">
                <div class="datepicker-wrapper">
                <Vue3Datepicker ref="datepicker" class="custom-date" @update:model-value="formatDate" :enable-time-picker="false" @cleared="datepicker.value = ''" @closed="datepicker.value = ''"/>
            </div>
                
            </div>

            <!-- <input type="date"> -->
            <div class="save-button d-flex align-items-center justify-content-center cursor-style"  @click="sendData"  data-bs-toggle="tooltip" data-bs-placement="right" title="AddTask" ref="tooltipIcon">
            <Icon icon="carbon:save" width="30" height="30" />
            </div>
        </div>

    </div>
</template>

<style>

.input-field {
    position: relative;  /* Make it a positioning context for absolute positioning of child elements */
}

.datepicker-wrapper {
    position: absolute;  /* Position it absolutely to place it directly behind the input */
    top: 0;
    left: 0;
    z-index: 0;  /* Send it behind the input */
}

.text-color {
    background-color: rgba(255, 255, 255, 0.5);  /* Add some transparency to the input to see the datepicker behind. */
}
.menu-style {
    display: flex;
    align-items: center;
    gap: 10px;
}



.text-color {
    color: white;
    background-color: transparent;
}
</style>
