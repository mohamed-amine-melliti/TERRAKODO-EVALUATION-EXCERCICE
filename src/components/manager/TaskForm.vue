<template>
  <form @submit.prevent="onSubmit" class="bg-white dark:bg-dark-grey rounded-lg p-1">
    <div class="p-5 pr-3 flex flex-col gap-6 max-h-[90vh] overflow-y-scroll">
      <div class="flex justify-between items-center">
        <h4 class="text-black dark:text-white font-bold text-lg">
          {{ managerStore.taskForm.edit ? 'Edit Task' : 'Add New TO-DO' }}
        </h4>
      </div>
      <BaseInput ref="inputTitle" v-model="form.task.title" inputName="Title" placeholder="e.g. Take coffee break" />
      <BaseTextarea v-model="form.task.description" inputName="Description"
        placeholder="e.g. It’s always good to take a break. This 15 minute break will  recharge the batteries a little." />

      <p class="text-medium-grey dark:text-white text-xs font-bold">Priority Level :</p>
      <Slider  :max ="5"  v-model="form.task.priority"/>

      <p class="text-medium-grey dark:text-white text-xs font-bold">  Date Of Completion :</p>
      <VueDatePicker v-model="form.task.dateOfCompletion"></VueDatePicker>


      <div class="flex flex-col gap-2">
        <p class="text-medium-grey text-xs font-bold">Status</p>
        <BaseSelect @onClickOption="updateColumn" :value="columnName" />
      </div>
      <ButtonPrimaryLarge type="submit">
        {{ managerStore.taskForm.edit ? 'Save Changes' : 'Create Task' }}
      </ButtonPrimaryLarge>
    </div>
  </form>
</template>
<style src="@vueform/slider/themes/default.css"></style>
<script setup>
import { ref, reactive, onBeforeUpdate } from 'vue'
import { useBoardsStore } from '@/stores/boards.js';
import { useManagerStore } from '@/stores/manager.js';
import BaseSelect from '@/components/form/BaseSelect.vue';
import BaseInput from '@/components/form/BaseInput.vue';
import BaseTextarea from '../form/BaseTextarea.vue';
import IconCross from '../icons/IconCross.vue';
import ButtonPrimaryLarge from '../buttons/PrimaryLarge.vue';
import ButtonSecondaryLarge from '../buttons/SecondaryLarge.vue';
import Slider from '@vueform/slider'
const boardsStore = useBoardsStore();
const managerStore = useManagerStore();
const columnName = ref('')

const inputTitle = ref(null)
const inputs = ref([])

const form = reactive({
  task: {
    title: '',
    description: '',
    subtasks: [{ title: '', isCompleted: false }, { title: '', isCompleted: false }],
    priority : 1 ,
    dateOfCompletion  : ''
  },
  column: 0
})
const subtaskPlaceholders = {
  0: 'e.g. Make coffee',
  1: 'e.g. Drink coffee & smile'
}
const deleteSubtask = (index) => {
  if (form.task.subtasks.length === 2) {
    form.task.subtasks[index].title = ''
    form.task.subtasks[index].isCompleted = false
  } else {
    form.task.subtasks.splice(index, 1)
  }
}
const addSubtask = () => {
  form.task.subtasks.push({ title: '', isCompleted: false })
}
const onSubmit = () => {
  if (validate()) {
    if (managerStore.taskForm.edit) {
      boardsStore.saveTaskChanges({ task: form.task, column: form.column })
    } else {
      boardsStore.getColumns[form.column].tasks.push(form.task)
    }
    managerStore.hideOverlay()
  }
}
const validate = () => {
  let valid = true
  if (form.task.title.trim().length === 0) {
    valid = false
    inputTitle.value.error = true
  }
  inputs.value.forEach((e, index) => {
    if (form.task.subtasks[index]?.title.trim().length === 0) {
      valid = false
      e.error = true
    }
  })
  return valid
}
const updateColumn = ({ index, name }) => {
  form.column = index
  columnName.value = name
}

//EDIT MODE
if (managerStore.taskForm.edit) {
  form.task = JSON.parse(JSON.stringify(boardsStore.getTask))
  form.column = JSON.parse(JSON.stringify(boardsStore.selectedColumn))
  columnName.value = JSON.parse(JSON.stringify(boardsStore.getColumnsNames[boardsStore.selectedColumn]))
} else {
  columnName.value = JSON.parse(JSON.stringify(boardsStore.getColumnsNames[0]))
}

onBeforeUpdate(() => {
  inputs.value = []
})
</script>

<script>
import VueDatePicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css'
export default {
  components: {
    Slider,
  },
  data() {
    return {
      value: 1,

    }
  }
}


</script>