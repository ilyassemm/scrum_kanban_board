<template>
  <div class="container mt-5">
	<p> <strong> Введите наименование задачи и дедлайн ее решения </strong> </p>
    <div class="row">
      <div class="col form-inline">
        <b-form-input
          id="input-2"
          v-model="newTask"
          required
          placeholder="Введите задачу"
          @keyup.enter="add"
        ></b-form-input> 
        <b-form-input
          id="input-3"
          v-model="deadline"
			type="date"
          required
          placeholder="Дедлайн"
          @keyup.enter="add"
        ></b-form-input>
        <b-button @click="add" variant="success" class="ml-3">Добавить</b-button>
      </div>
    </div>
<!-- БЭКЛОГ --> 
    <div class="row mt-5">
      <div class="col-3">
        <div class="p-2 alert alert-secondary">
          <h3>Бэклог задач</h3>
          <draggable
            class="list-group kanban-column"
            :list="arrBackLog"
            group="tasks"
          >
            <div
              class="list-group-item"
              v-for="element in arrBackLog"
              :key="element.name"
            >
              {{ element.name}} до {{ element.deadline[8] }}{{ element.deadline[9] }}.{{ element.deadline[5] }}{{ element.deadline[6] }}
			<button class="button_delete" v-on:click="arrBackLog.splice(index, 1)">-</button>
            </div>
          </draggable>
		<b-button @click="delBackLog" variant="secondary" class="ml-3">Удалить все задачи</b-button>
        </div>
      </div>
<!-- В РАБОТЕ --> 
      <div class="col-3">
        <div class="p-2 alert alert-danger">
          <h3>В работе</h3>
          <draggable
            class="list-group kanban-column"
            :list="arrInProgress"
            group="tasks"
          >
            <div
              class="list-group-item"
              v-for="element in arrInProgress"
              :key="element.name"
            >
              {{ element.name}} до {{ element.deadline[8] }}{{ element.deadline[9] }}.{{ element.deadline[5] }}{{ element.deadline[6] }}
			<button class="button_delete" v-on:click="arrInProgress.splice(index, 1)">-</button>
            </div>
          </draggable>
		<b-button @click="delProgress" variant="danger" class="ml-3">Удалить все задачи</b-button>
        </div>
      </div>
<!-- ТЕСТИРУЮТСЯ--> 
      <div class="col-3">
        <div class="p-2 alert alert-warning">
          <h3>Тестируются</h3>
          <draggable
            class="list-group kanban-column"
            :list="arrTested"
            group="tasks"
          >
            <div
              class="list-group-item"
              v-for="element in arrTested"
              :key="element.name"
            >
              {{ element.name}} до {{ element.deadline[8] }}{{ element.deadline[9] }}.{{ element.deadline[5] }}{{ element.deadline[6] }}
			<button class="button_delete" v-on:click="arrTested.splice(index, 1)">-</button>
            </div>
          </draggable>
		<b-button @click="delTest" variant="warning" class="ml-3">Удалить все задачи</b-button>
        </div>
      </div>
<!-- ГОТОВО--> 
      <div class="col-3">
        <div class="p-2 alert alert-success">
          <h3>Готово</h3>
          <draggable
            class="list-group kanban-column"
            :list="arrDone"
            group="tasks"
          >
            <div
              class="list-group-item"
              v-for="element in arrDone"
              :key="element.name"
            >
              {{ element.name}} до {{ element.deadline[8] }}{{ element.deadline[9] }}.{{ element.deadline[5] }}{{ element.deadline[6] }}
			<button class="button_delete" v-on:click="arrDone.splice(index, 1)">-</button>
            </div>
          </draggable>
		<b-button @click="delDone" variant="success" class="ml-3">Удалить все задачи</b-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";

export default {
  name: "Планировщик",
  components: {
    draggable
  },
  data() {
    return {
      newTask: "",
	deadline: "",
	arrBackLog: [],
      arrInProgress: [],
      arrTested: [],
      arrDone: [],
    };
  },
  methods: {
    // добавить задачу
     add: function() {
      if (this.newTask) {
        this.arrBackLog.push({ name: this.newTask, deadline: this.deadline });
		var	existing = JSON.parse(localStorage.getItem('arrBackLog')) || [];
		existing.push({ name: this.newTask });
		localStorage.setItem('arrBackLog', JSON.stringify(existing));
		this.newTask = "";
      }
    },
	// загрузить задачи
	load() {
		this.arrBackLog = JSON.parse(localStorage.getItem('arrBackLog')) || [];
		this.arrTested = JSON.parse(localStorage.getItem('arrTestedLength')) || [];
		this.arrDone = JSON.parse(localStorage.getItem('arrDoneLength')) || [];
		this.arrInProgress = JSON.parse(localStorage.getItem('arrInProgressLength')) || []; 
	},

	// удаление всех задач из конкретного блока

	delBackLog() {
		this.arrBackLog = []
		localStorage.setItem('arrBackLog', [])
	},

	delProgress() {
		this.arrTested = []
		localStorage.setItem('arrInProgressLength', [])
	},

	delTest() {
		this.arrDone = []
		localStorage.setItem('arrTestedLength', [])
	},

	delDone() {
		this.arrInProgress.length = 0
		localStorage.setItem('arrDoneLength', [])
	},

	
  },
	// вспомогательные функции для вычисления длин массивов
  computed: { 
		arrBackLogLength() {
      return this.arrBackLog.length
    },

		arrTestedLength() {
      return this.arrTested.length
    },
		arrDoneLength() {
      return this.arrDone.length
    },
		arrInProgressLength() {
      return this.arrInProgress.length
    },
		
	},

  watch: {
	// Если длина изменилась, сразу же фиксируем изменения

    arrBackLogLength(val, oldVal) {
		console.log(val, oldVal)
		localStorage.setItem('arrBackLog', JSON.stringify(this.arrBackLog));
    },

    arrTestedLength(val, oldVal) {
		console.log(val, oldVal)
		localStorage.setItem('arrTestedLength', JSON.stringify(this.arrTested));
    },

    arrDoneLength(val, oldVal) {
		console.log(val, oldVal)
		localStorage.setItem('arrDoneLength', JSON.stringify(this.arrDone));
    },

    arrInProgressLength(val, oldVal) {
		console.log(val, oldVal)
		localStorage.setItem('arrInProgressLength', JSON.stringify(this.arrInProgress));
    },

  },

  beforeMount() {
	this.load();
  },

    mounted() {
        document.title = 'Планировщик задач'
    },
};
</script>

<style>
/* light stylings for the kanban columns */
.kanban-column {
  min-height: 300px;
}

.button_delete {
  background-color: #913831;
  border: none;
  color: white;
  padding: 5px;
  text-align:center;
  text-decoration:none;
  display:inline-block;
  font-size:16px;
  cursor: pointer;
  border-radius: 50%;
  line-height: 6px;
} 
</style>
