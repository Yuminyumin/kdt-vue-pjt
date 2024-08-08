<template>
    <div class="container">
        <h2>Todo Read View</h2>
        
        <div v-if="loading">
            Loding.......
        </div>

        <form   v-else
                @submit.prevent="onUpdate">
            <div class="row">
                <div class="col-6">
                    <div class="form-group">
                        <label>Subject</label>
                        <input  type="text" 
                                class="form-control"
                                v-model="todo.subject">
                    </div>
                    
                </div>
                <div class="col-6">
                    <div class="form-group">
                        <label>State</label>
                        <div>
                            <button class="btn"
                                    :class="todo.completed ? 'btn-primary' : 'btn-danger'"
                                    @click="toggleState"
                                    type="button"> 
                                    {{ todo.completed ? 'Completed' : 'InComplete' }}
                            </button>
                        </div>
                    </div>
                </div>    
            </div>    

            <button class="btn btn-outline-dark" type="submit">Update</button>

            <!-- cancel 버튼 클릭시  path /todos 연결된 컴포넌트로 화면전환 -->
            <button class="btn btn-outline-danger ml-2"
                    @click.stop="moveToTodos">Cancel</button>   

        </form>

    </div>
</template>

<script>

import { useRoute , useRouter } from 'vue-router' ; 
import { ref }      from 'vue';
import axios        from 'axios' ; 
export default {
    setup() {
        const route  = useRoute();
        const router = useRouter();
        
        console.log("debug >>> TodoView params , " , route.params) ; 
        console.log("debug >>> TodoView params , " , route.params.id) ;  

        const loading = ref(true);
        const todo    = ref(null);
        /*
        update 클릭시 변경된 상태값을 json-server에 변경하고 
        화면을 path todos 이동 
        */
        const onUpdate = async () => {
            try {
                await axios.patch(`http://localhost:3000/todos/${route.params.id}` , {
                    completed : todo.value.completed , 
                    subject   : todo.value.subject      
                });
                moveToTodos();
            } catch( err ) {
                console.log(err); 
            } 
        }

        /*
        button 이벤트에 의해서 호출함수
        이벤트 핸들러에서 
        todo의 completed 값을 변경하는 구현!!!
        */
        const toggleState = () => {
            todo.value.completed = !todo.value.completed ; 
        }

        const getTodo = async () => {
            try {
                const response = await axios.get(`http://localhost:3000/todos/${route.params.id}`); 
                console.log( response.data ); 
                todo.value = response.data ;
                loading.value = false ;
            } catch ( err ) {
                console.log( err );
            }
        }
        getTodo();

        const moveToTodos = () => {
            router.push("/todos");
        }

        return {
            loading,
            todo,
            moveToTodos,
            toggleState,
            onUpdate
        }
    }
}
</script>

<style>

</style>