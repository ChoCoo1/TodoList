<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-form
          ref="form"
          lazy-validation
          class ="text-center"
        >
        <v-text-field v-model="newTodo.title"
          :counter="64" :rules="newTodo.titleRules"
          label="任务名称"
          required
        ></v-text-field>

          <v-textarea
              v-model="newTodo.description"
              :rules="newTodo.descriptionRules"
          label="任务描述"
          aria-autocomplete="Description"
          ></v-textarea>

        <v-checkbox
            v-model="newTodo.is_complete"
          label="任务是否完成"
        ></v-checkbox>

        <v-btn
          color="error"
          class="mr-4"
          @click="reset"
        >
          清除
        </v-btn>

          <v-btn
          color="success"
          class="mr-4"
          :disabled="!newTodo.title"
          @click="add"
        >
          添加
        </v-btn>
      </v-form>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12" >
        <h3>Todo List:</h3>
        <v-list>
        <v-list-item-group color="primary" multiple v-model="selected">
          <v-list-item v-for="item in todolist" :key="item.title" @click="updateStatus(item)">
            <v-list-item-icon>{{item.id}}</v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title ><strong>{{item.title}}</strong></v-list-item-title>
              <v-list-item-subtitle>{{item.description}}</v-list-item-subtitle>
            </v-list-item-content>
              <v-list-item-action>
                <v-checkbox :input-value="item.is_complete"
                color="primary"
                >
                </v-checkbox>
              </v-list-item-action>
          </v-list-item>
        </v-list-item-group>
        </v-list>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios';
  export default {
    data: () => ({
      newTodo:{
        title: "",
        description: "",
        titleRules:[
          (v) =>!!v || "必须输入项目名称",
          (v) => (v && v.length <= 64) || '任务名称不超过32个字',
        ],
        descriptionRules:[
          (v) => !!v ||'任务描述必须填写',
        ],
        is_complete: false,
        user: 1,
      },
      selected: [],
      todolist: [],
      url: "http://127.0.0.1:8000/api/todo/",
    }),
    mounted(){
      this.selected=[];
      this.getTodos();
    },
    methods:{
      getTodos(){
        axios.get(`${this.url}`).then((response) =>{
          this.todolist = response.data;
          response.data.forEach((element,index) => {
            if(element.is_complete) this.selected.push(index);
          })
        })
      },
      reset(){
        this.$refs.form.reset();
      },
      add(){
        let data =this.newTodo;
        axios.post(this.url,data).then((response) =>{
          this.selected = [];
          this.getTodos();
        })
      },
      updateStatus(item){
        item.is_complete = !item.is_complete;
        const url = `${this.url}${item.id}/`;
        let data ={
          is_complete: item.is_complete,
        };
        axios.patch(url,data).then((response)=>{
          this.selected = [];
          this.getTodos();
        })
      },

    }
  }
</script>
