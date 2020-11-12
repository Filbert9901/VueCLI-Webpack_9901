<template>
 <v-main class="list">
  <h3 class="text-h3" font-weight-medium mb-5>To Do List UGD</h3>

  <div class="d-flex flex-xl-row">
  <v-card width="600px">
    <v-card-title>
     <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
        ></v-text-field>
        <v-spacer></v-spacer>
        <v-btn color="success" dark @click="dialog=true">
         Tambah
        </v-btn>
    </v-card-title>

    <v-data-table :headers="headers" :items="todos" :search="search">
        <template v-slot:[`item.actions`]="{item}">
            <v-icon left  @click="detailItem(item)" color="blue">
                mdi-note-outline
                value=item
            </v-icon>
        </template>

         <template v-slot:[`item.priority`]="{item}">
            <v-chip class="ma-2" label color="red" outlined v-if="item.priority=='Penting'">
                {{item.priority}}
            </v-chip>
            <v-chip class="ma-2" label color="success" outlined v-else-if="item.priority=='Tidak penting'">
                {{item.priority}}
            </v-chip>
            <v-chip class="ma-2" label color="primary" outlined v-else>
                {{item.priority}}
            </v-chip>
        </template>
    </v-data-table>
    </v-card>
    
    <div v-show="details" class="ml-3 mt-3">
       <div class="d-flex flex-row mb-6">
           <h1>{{detailsTodo.task}}</h1>
            <v-chip class="ma-2" color="red" label  v-if="detailsTodo.priority=='Penting'" text-color="white">
               {{detailsTodo.priority}}
            </v-chip>
            <v-chip class="ma-2" color="success" label  v-else-if="detailsTodo    .priority=='Tidak penting'" text-color="white">
                {{detailsTodo.priority}}
            </v-chip>
            <v-chip class="ma-2" color="primary" label  v-else text-color="white">
                {{detailsTodo.priority}}
            </v-chip>
       </div>
        <p >{{detailsTodo.note}}</p>
       
            <br>
        <v-icon left  @click="editItem(detailsTodo.index)" color="blue">
            mdi-pencil
        </v-icon>
          <v-icon left  @click="deleteItem(detailsTodo.index)" color="red darken-1">
            mdi-delete
        </v-icon>
    </div>
    
    </div>

    <v-dialog v-model="dialog" persistent max-width="600px">
        <v-card>
            <v-card-title>
                <span class="headline">Form Todo</span>
            </v-card-title>

            <v-card-text>
                <v-container>
                    <v-text-field
                    v-model="formTodo.task"
                    label="Task"
                    required
                    ></v-text-field>

                    <v-select
                    v-model="formTodo.priority"
                    :items="['Penting','Biasa','Tidak penting']"
                    label="Priority"
                    required
                    ></v-select>

                    <v-textarea
                    v-model="formTodo.note"
                    label="Note"
                    required
                    ></v-textarea>
                </v-container>
            </v-card-text>

            <v-card-actions>            
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="cancel">
                    Cancel
                </v-btn>
                <v-btn v-if="editing==false" color="blue darken-1" text @click="save">
                    Save
                </v-btn>        
                 <v-btn v-show="editing" color="blue darken-1" text @click="edit" >
                    Edit
                </v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="deleted" persistent max-width="400px">
      <v-card>
        <v-card-title class="headline">
         Yakin ingin menghapus?
        </v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="green darken-1"
            text
            @click="deleted = false"
          >
            Tidak
          </v-btn>
          <v-btn
            color="red lighten-2"
            text
            @click="hapus"
          > Ya
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</v-main>
</template>

<script>
export default {
  name: "ListUGD",
  data(){
      return{
          search:null,
          dialog:false,
          indexEdit:null,
          indexDelete:null,
          editing:false,
          details:false,
          deleted:false,
          headers:[
            {
                text: "Task",
                align: "start",
                sortable: true,
                value: "task",
            },
            { text: "Priority", value: "priority"},
            { text: "Actions", value: "actions"},
          ],
          todos: [
              {
                  task: "bernafas",
                  priority: "Penting",
                  note: "huffttt",
              },
              {
                  task: "nongkrong",
                  priority: "Tidak penting",
                  note: "bersama teman",
              },
              {
                  task: "masak",
                  priority: "Biasa",
                  note: "masak air 500ml",
              },
          ],
          formTodo: {
              task: null,
              priority: null,
              note: null,
          },
          detailsTodo:{
              task: "",
              priority: "",
              note: "",
              index:null,
          },
          hapusToDos:[],
      };
  },methods: {
      save(){
          this.todos.push(this.formTodo);
          this.resetForm();
          this.dialog = false;
      },
       cancel(){
           this.resetForm();
           this.dialog = false;
           this.editing = false;
       },
        resetForm(){
            this.formTodo = {
              task: null,
              priority: null,
              note: null,
            };
        },
         deleteItem(index){
            this.deleted=true;
            this.indexDelete=index;
            
         },
         hapus(){
            this.todos.splice(this.indexDelete,1);
            this.deleted=false;
            this.details=false;
         },
         detailItem(item){
             this.details=true;
             const index = this.todos.indexOf(item);
             this.detailsTodo.task=this.todos[index].task;
             this.detailsTodo.priority=this.todos[index].priority;
             this.detailsTodo.note=this.todos[index].note;
             this.detailsTodo.index = index;
         },
          editItem(index){
              this.dialog= true;
              this.editing= true;
              this.indexEdit=index;
              this.formTodo.task=this.todos[index].task;
              this.formTodo.priority=this.todos[index].priority;
              this.formTodo.note=this.todos[index].note;
          },	
          edit(){   
                const test = this.indexEdit;
                this.todos.splice(test,1);
                this.todos.splice(test,0,this.formTodo);
                this.resetForm();
                this.dialog= false;
                this.editing= false;
                this.detailsTodo.task=this.todos[test].task;
                this.detailsTodo.priority=this.todos[test].priority;
                this.detailsTodo.note=this.todos[test].note;
                this.details=false;
                
           },
            
  },
};
</script>


