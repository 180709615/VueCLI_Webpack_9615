
<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
        
        <v-card>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>
                <v-spacer></v-spacer>

                <v-select
                    v-model ="selectedPriority"
                    :items="['Penting', 'Tidak penting']"
                    @input="sortBy(selectedPriority)"    
                ></v-select>
                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
               

        
                

            </v-card-title>


            <v-data-table 
            :headers="headers" 
            :items="todos" 
            :search="search" 
            :expanded.sync="expanded" 
            item-key="note" 
            show-expand 
            show-select
            :data="todos"
            v-model="selected"
            default-sort="priority"
            
            
            class="elevation-1">
                <template v-slot:[`item.actions`]="{ item }">
                    <v-icon color="success" small class="mr-2"  @click="editItem(item)">
                        mdi-pencil
                    </v-icon>
                    <v-icon color="red" small @click="deleteItem(item)">
                        mdi-delete
                    </v-icon>
                </template>

                <template v-slot:item.check="{ item }" @click="cekItem(item)"> </template>

                
                <template v-slot:expanded-item="{ headers,item }" >
                    <td :colspan="headers.length" class="mr-2">
                        <v-row :align="start">
                            <h2>Note :</h2>
                        </v-row>
                        <v-row :align="start">  
                            {{ item.note }}
                        </v-row>
                     
                    </td>
                </template>

                <template v-slot:[`item.priority`]="{ item }">
                    <td>
                            <v-chip v-if="item.priority == 'Penting'" color="red" outlined>
                                {{ item.priority }}
                            </v-chip>
                            <v-chip v-else-if="item.priority == 'Biasa'" color="success" outlined>
                                {{ item.priority }}
                            </v-chip>
                            <v-chip v-else color="primary" outlined>
                                {{ item.priority }}
                            </v-chip>
                    </td>                 
                </template> 
                
            </v-data-table>
        </v-card>

        <v-card class="elevation-1 mt-3">
            <v-card-title>
                <h4> Checklist </h4>
            </v-card-title>
            <v-card-text>
                <div v-show="selected">
                    <ul>
                        <li align="start" v-for="(todo,index) in selected" :key="index">{{ todo.task }}</li>
                    </ul>
                    <v-btn color="red" dark @click="deleteSemua()">
                        Delete Semua
                </v-btn>
                </div>
            </v-card-text>
        </v-card>


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
                                :items="['Penting', 'Biasa', 'Tidak penting']"
                                label="Priority"
                                required
                            ></v-select>

                            <v-text-field
                                v-model="formTodo.note"
                                label="Note"
                                required
                            ></v-text-field>
                        
                        </v-container>
                    </v-card-text>
                    <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn color="blue darken-1" text @click="cancel">
                            Cancel
                        </v-btn>
                        <v-btn color="blue darken-1" text @click="save">
                        Save
                        </v-btn>
                    </v-card-actions>
                </v-card>

        </v-dialog>

         <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="headline">Delete Item ini?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialogDelete = false">Cancel</v-btn>
              <v-btn color="red darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
        
    </v-main>
</template>
<script>
export default {
    name: "List",
    data() {
        return {
            search: null,
            dialog: false,
            dialogDelete : false,
            selected:[],
            editedIndex: -1,
            selectedPriority:"",
            expanded: [],
            singleExpand: false,
            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                //{ text: "Note", value: "note" },
                { text: "Actions", value: "actions" },
                { text: "Check", value:"data-table-select"},
                
            ],
            todos: [
                {
                    task: "bernafas",
                    priority: "Penting",
                    idP:1,
                    note: "huffttt",
                },
                {
                    
                    task: "nongkrong",
                    priority: "Tidak penting",
                    idP:3,
                    note: "bersama tman2",
                },
                {
                    task: "masak",
                    priority: "Biasa",
                    idP:2,
                    note: "masak air 500ml",
                },
            ],
            
            formTodo: {
                task: null,
                priority: null,
                idP: null,
                note: null,
            },
        };
    },
    
    methods: {

        sortBy(item){
            if(item == "Penting")
                this.todos.sort((a,b) => a.idP < b.idP ? -1 : 1)
            else
                this.todos.sort((a,b) => a.idP > b.idP ? -1 : 1)
        },
        
        save() {

            if(this.formTodo.priority == "Penting"){
                this.formTodo.idP =1;
            }else if(this.formTodo.priority == "Biasa"){
                this.formTodo.idP =2;
            }else if(this.formTodo.priority == "Tidak penting"){
                this.formTodo.idP =3;
            }
            console.log(this.formTodo.note)
            console.log(this.formTodo.priority)
            console.log(this.formTodo.idP)

            if (this.editedIndex > -1) {
                Object.assign(this.todos[this.editedIndex], this.formTodo)
                this.editedIndex = -1;
            } else {
                this.todos.push(this.formTodo)
                
            }
            this.editedIndex = -1;
            //this.todos.push(this.formTodo);
            this.resetForm();
            this.dialog = false;
        },
        cancel() {
            this.resetForm();
            this.dialog = false;
        },
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        editItem (item) {
            this.editedIndex = this.todos.indexOf(item)
            this.formTodo = Object.assign({}, item)
            this.dialog = true
        },

      deleteItem (item) {
            this.editedIndex = this.todos.indexOf(item)
            this.formTodo = Object.assign({}, item)
            this.dialogDelete = true
            
        },

      deleteItemConfirm () {
            this.todos.splice(this.editedIndex, 1)
            this.dialogDelete = false;
            this.editedIndex = -1
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        deleteSemua(){
            
            var i=0
            var length=this.selected.length
            console.log(length)
            for(i = 0;i<length;i++)
            { 
                
                var index = this.todos.indexOf(this.selected[i])
                
                this.todos.splice(index, 1)
                this.selected.splice(i,1);
            
            }
            this.selected = []
        },
    },
};
</script>