<template>
  
  <div class="container">

    <div class="input-part">
      <input class="inp" type="text" placeholder="add items" @keyup.enter="addTodo">
      <button class="btnAdd" @click="addTodoWithBtn"> Add </button>
    </div>

    <div class="list-part">
      <div v-if="itemList.length > 0">
        <ul class="list">
          <li class="listEl" v-for="item in itemList" :key="item"> 
            <div class="item" @click="completed(item, $event)"> {{ item.itemName }} </div>
            <div class="trash" @click="deleteTodo(item)"> X </div>  
          </li>
        </ul>
        <div class="result-table">
          <div class="completed"> <span> completed: {{ numOfCompleted() }} </span> </div>
          <div class="uncompleted"> <span> uncompleted: {{ numOfUncompleted() }} </span> </div>
        </div>
      </div>
      <div v-else class="warning">
        <div>There is no item!</div>
      </div>
    </div>

  </div>


</template>

<script>

import axios from 'axios';

export default {
  name: 'App',

  data(){
    return {
      itemList: [],
    }
  },

  mounted(){
    axios.get('http://localhost:3000/datas')
      .then(response => this.itemList = response.data || []);
  },

  methods: {

    addTodo(event){
      let data = {
        itemName: event.target.value,
        isCompleted: false
      }
      if(data.itemName != ''){
        axios.post('http://localhost:3000/datas', data)
          .then(response => this.itemList.push(response.data) );
      }
      event.target.value = '';
    },

    addTodoWithBtn(event){
      let data = {
        itemName: event.target.parentNode.firstChild.value,
        isCompleted: false,
      }
      if(data.itemName != ''){
        axios.post('http://localhost:3000/datas', data)
          .then(response => this.itemList.push(response.data) );
      }
      event.target.parentNode.firstChild.value = '';
    },

    deleteTodo(delItem){
      console.log('delt')
      axios.delete(`http://localhost:3000/datas/${delItem.id}`)
        .then(response => {
          this.itemList = this.itemList.filter(item => item != delItem);
          console.log(response)
        });
    },

    completed(item, event){
      console.log(event);
      event.target.classList.toggle('completed');
      (item.isCompleted) ? item.isCompleted = false : item.isCompleted = true;
    },

    numOfCompleted(){
      return this.itemList.filter(item => item.isCompleted == true).length;
    },

    numOfUncompleted(){
      return this.itemList.filter(item => item.isCompleted == false).length;
    }

  }

}
</script>


