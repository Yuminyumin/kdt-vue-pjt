<template>
	<div class="container">
		<h2>Login Page</h2>
        <form @submit.prevent="onLogin">
		<div class="mb-3">
			<label for="exampleId" class="form-label">ID</label>
			<input
				type="text"
				class="form-control"
				id="exampleId"
				placeholder="아이디를 입력하세요"
                v-model="user.id"
			/>
		</div>
		<div class="mb-3">
			<label for="examplePassword" class="form-label">Password</label>
			<input
				type="password"
				class="form-control"
				id="examplePassword"
				placeholder="암호를 입력하세요"
                v-model="user.password"
			/>
		</div>
		<div class="d-grid gap-2">
			<button class="btn btn-primary" type="submit">Login</button>
			<button class="btn btn-primary" type="button">Cancel</button>
		</div>
    </form>
	</div>
</template>

<script>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from "@/axios";

export default {
	setup() {
        let user = ref({});
        const router = useRouter();
        const onLogin = async () => {
            console.log("debug >>> login create btn click") ; 
            try {
                // db.json 통신하고 정상적인 상황이면 router push 이용해서 화면 전환(todos)
                let response = await axios.get(`users` , {
                    params : {
                        id: user.value.id,
                        password: user.value.password
                    }
                });
                if (response.data.length > 0) {
                    alert('Login successful!');
                }
                user.value = response.data[0] ;
                
                router.push({
                    name: 'Todos',
                });
            } catch( err ) {
                console.log( err );
            }
        }
        

        return{
            user,
            onLogin,
        }
    },
};
</script>

<style></style>