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
                v-model="searchPriority"
                :items="['Penting.', 'Biasa', 'Tidak Penting']"
                label="All Priority"
                single-line
                hide-details
                ></v-select>

                <v-spacer></v-spacer>

                <v-btn color="succsess" dark @click="dialog = true">
                    Tambah
                </v-btn>
            </v-card-title>

            <v-data-table :headers="headers" :items="todos" :search="searchPriority">
                <template v-slot:[`item.actions`]="{ item }">
                    <v-icon small class="pencil mr-2" @click="editItem(item)" color="success">  
                        {{ icons.mdiPencil }}
                    </v-icon>

                    <v-icon small class="bin mr-2" @click="deleteItem(item)" color="red">   
                        {{ icons.mdiDelete }}
                    </v-icon>
                </template>

                <template v-slot:[`item.checkbox`]="{ item }">
                    <input
                    type="checkbox"
                    v-model="checkedNames"
                    :value="item"
                    />
                </template>
            </v-data-table>
        </v-card>

        <v-card style="marginTop: 20px" v-if="checkedNames.length">
            <v-card-title>
                <h5>Delete Multiple : </h5>
            </v-card-title>

            <v-card-text>
                <ul>
                    <li v-for="checkedName in checkedNames" :key="checkedName.task">
                        {{ checkedName.task }}
                    </li>
                </ul>
            </v-card-text>

            <v-card-actions>
                <v-btn depressed color="error" small @click="dialogdeleteAll = true">
                    Hapus Semua
                </v-btn>
            </v-card-actions>
        </v-card>

        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">From Todo</span>
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
                        :items="['Penting.', 'Biasa', 'Tidak Penting']"
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

                    <v-btn color="blue darken-1"
                    text @click="cancel">
                        Cancel
                    </v-btn>

                    <v-btn color="blue darken-1"
                    text @click="updateItem">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogdelete" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Apakah Anda Yakin Untuk Hapus? </span>
                </v-card-title>

                <v-card-actions>
                    <v-spacer></v-spacer>

                    <v-btn color="blue darken-1" 
                    text @click="cancel">
                        Tidak
                    </v-btn>

                    <v-btn color="blue darken-1" 
                    text @click="deleteItemYes">
                        Ya
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogdeleteAll" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Apakah Anda Yakin Untuk Hapus Beberapa? </span>
                </v-card-title>

                <v-card-actions>
                    <v-spacer></v-spacer>

                    <v-btn color="blue darken-1" 
                    text @click="cancel">
                        Tidak
                    </v-btn>

                    <v-btn color="blue darken-1" 
                    text @click="deleteAll">
                        Ya
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-main>
</template>

<script>
import {
    mdiPencil,
    mdiDelete,
} from '@mdi/js'

export default {
    name: "List",
    data(){
        return {
            search: null,
            dialog: false,
            dialogdelete: false,
            dialogdeleteAll: false,
            searchPriority: null,
            x: null,
            icons: {
                mdiPencil,
                mdiDelete,
            },

            checkedNames: [],

            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Note", value: "note" },
                { text: "Actions", value: "actions" },
                { text: "CheckBox", value: "checkbox" },
            ],

            todos: [
                {
                    task: "bernafas",
                    priority: "Penting.",
                    note: "huffttt",
                },
                {
                    task: "nongkrong",
                    priority: "Tidak Penting",
                    note: "bersama tman2",
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
        };
    },
    
    methods: {
        save(){
            this.todos.push(this.formTodo);
            this.resetForm();
            this.dialog = false;
        },

        cancel(){
            this.resetForm();
            this.dialogdelete = false;
            this.dialog = false;
            this.dialogdeleteAll = false;
            this.x = null;
        },

        resetForm(){
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        
        deleteItem(item){
            this.dialogdelete = true;
            this.x = item;
            
        },

        deleteItemYes(){
            this.todos = this.todos.filter(todo => todo !== this.x);
            this.x = null;
            this.dialogdelete = false;
        },

        editItem(item){
            this.formTodo = {
                task: item.task,
                priority: item.priority,
                note:  item.note,
            }
            this.x = item;
            this.dialog = true;
        },

        updateItem(){
            this.save();
            this.todos = this.todos.filter(todo => todo !== this.x);
            this.x = null;
        },

        deleteAll(){
            for(this.i = 0; this.i <= this.checkedNames.length; this.i++){
                this.todos = this.todos.filter(todo => todo !== this.checkedNames[this.i]);
            }
            this.checkedNames = [];
            this.dialogdeleteAll =  false;
        },
    },
};
</script>