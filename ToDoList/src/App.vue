<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref(null)
const input_date= ref('')
const input_time = ref('');
const input_notes = ref('');

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})



const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	const newTodo = {
		content: input_content.value,
		category: input_category.value,
		due: input_date.value, // Save the selected date
    time: input_time.value,
    notes: input_notes.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	};

	todos.value.push(newTodo);

	// Save todos to localStorage
	localStorage.setItem('todos', JSON.stringify(todos.value));
}


const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				What's up, <input type="text" id="name" placeholder="(Your Name Here)" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3>CREATE A TODO</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input 
					type="text" 
					name= "content" 
					id="content" 
					placeholder="e.g. make a video"
					v-model="input_content" />
				
				<h4>Pick a category: </h4>


				<div class="options">
					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Business</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>

				</div>

        <div class="note">
          <h4>Add any notes here: </h4>
          <input type="text" v-model="input_notes">
        </div>
          <div class="dateTime">
        <div class="dueDate">
          <h4>When is it due?</h4>
          <input type="date" v-model="input_date">
        </div>
        <div class="time">
          <h4>What time?</h4>
          <input type="time" v-model="input_time">
        </div>
    </div>
				<input type="submit" class="submit-btn" value="Add Task" />
  

			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc"
         :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
           <li> <input type="text" v-model="todo.content" class="{{ todo.category }}" />
            <p><strong class="bold">Due Date:</strong> {{ todo.due }} @ {{ todo.time }}
            </p>  <small> Notes: {{ todo.notes }}  </small> 
            <!-- Display the due date from the input -->
          
          </li>
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>