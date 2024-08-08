<template>
    
    <input  class="form-control"
            type="text"
            v-model="searchTxt"
            placeholder="Search..."
            />


    <TodoForm @add-todo="addTodo"/>

    <div style="color :red ;">{{ error }}</div>

    <TodoList   :todos="filteredTodos"
                @toggle-todo="toggleTodo"
                @delete-todo="onDelete"/>
    



</template>

<script>

import { ref , computed } from 'vue' ; 
import TodoForm from './components/todo/TodoForm.vue';
import TodoList from './components/todo/TodoList.vue';
import axios    from 'axios' ; 

export default {
    components : {
        TodoForm,
        TodoList,
    },
    setup() {
        const todo  = ref('');
        const todos = ref([]);
        const searchTxt = ref('');
        const error = ref('');

        /*
        getTodos 로딩시 
        get - select
        
        */
        const getTodos = async () => {
            try{
                const response = await axios.get("http://localhost:3000/todos");
                console.log(response.data);
                todos.value = response.data;
            } catch (err){
                console.log(err);
                error.value = "something went wrong..";
            }
            
        }
        getTodos();
/*      
        const onDelete = (index) => {
            console.log("debug >>>> todos delete index , " , index ); 
            todos.value.splice(index, 1); 
        }
*/
         // 삭제를 위한 비동기 함수 구문
        const onDelete = async (index) => {
            console.log(" dledte index : ", index);
            error.value = "";
            const id = todos.value[index].id; //하위 컴포넌트와 연결된 porps의 todos에서 삭제 벼튼 이벤트가 발생하면 해당 todos의 값을 가져와 id를 넣어준다.
            try {
                //axios 통신을 통해서 키 값을 전달하고 json-server 쪽에서 삭제
                const response = await axios.delete(
                  `http://localhost:3000/todos/${id}`
                );
                console.log(" todos deldte response , ", response);
                todos.value.splice(index, 1);
            } catch (err) {
                console.log(err);
                error.value = "something went wrong..";
            }
        };

        const filteredTodos = computed(() => {
            if (searchTxt.value) {
                return todos.value.filter(todo => {
                    return todo.subject.includes(searchTxt.value);
                });
            }
            return todos.value;
        }) ; 

        /*
        기존 배열에 담았던 객체 정보를 
        axios 통신을 통해서 json-server db.json 에 저장하도록 변경 
        const addTodo = (data) => {
            todos.value.push(data); 
        }
        *//*
        const addTodo = (data) => {
            error.value = '' ;
            axios.post('http://localhost:3000/todos' , {
                id : Date.now(),
                subject : data.subject , 
                completed : data.completed 
            }).then( response => {
                console.log( response );
                console.log( response.data );
                todos.value.push( response.data ); 
            }).catch( err => {
                console.log( err  );
                error.value = 'Something went wrong...'; 
            });
        }*/

        const addTodo = async (data) =>{
            try{
                const response = await axios.post( 'http://localhost:3000/todos',{
                    id : Date.now(),
                    subject : data.subject , 
                    completed : data.completed 
                });
                todos.value.push(response.data);

            } catch (err){
                console.log( err  );
                error.value = 'Something went wrong...';
            }
            
        }

        /*
        const toggleTodo = (index) => {
            todos.value[index].completed = !todos.value[index].completed;
        }
            update table set completed =? wehre id =? ;
            axios patch 이용해서
        */
        const toggleTodo = async (index) => {
            const key = todos.value[index].id;
            const updateData = {
                completed : !todos.value[index].completed
            };
            try{
                const response = await axios.patch(`http://localhost:3000/todos/${key}`,updateData);
                console.log(response.data)
                todos.value[index].completed = response.data.completed;
            } catch (err){
                console.log( err  );
                error.value = 'Something went wrong...';
            }
        }

        return {
            todo, 
            todos,
            onDelete,
            searchTxt,
            filteredTodos,
            addTodo,
            toggleTodo,
            error,
        }
    }
}
</script>

<style>

</style>