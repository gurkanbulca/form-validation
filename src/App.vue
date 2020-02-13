<template>
  <div id="app">
    <div class="container mt-5">
      <div class="row">
        <div class="col-sm-12 d-flex justify-content-center">
          <h4 class="text-justify">Form Control with Vuelidate</h4>
        </div>
        <div class="col-12 d-flex justify-content-center">
          <form class="p-3 shadow" style="width: 350px;">
            <div class="form-group">
              <label for="col-sm-2 col-form-label">Email</label>
              <input
                @blur="$v.email.$touch()"
                type="email"
                class="form-control"
                :class="{'is-invalid':$v.email.$error,'is-valid':(!$v.email.$error && $v.email.required && $v.email.email)}"
                v-model="email"
                placeholder="Enter your e-Mail adress..."
              />
              <small
                v-if="$v.email.$error && !$v.email.required"
                class="form-text text-danger"
              >This field is required!</small>
              <small
                v-if="!$v.email.email && $v.email.$error"
                class="form-text text-danger"
              >Please, enter a valid e-mail adress!</small>
            </div>
            <div class="form-group">
              <label for="col-sm-2 col-form-label">Password</label>
              <input
                @blur="$v.password.$touch()"
                type="password"
                class="form-control"
                :class="{'is-invalid':$v.password.$error,'is-valid':!$v.password.$invalid}"
                v-model="password"
                placeholder="Enter your password..."
              />
              <small
                v-if="$v.password.$error && !$v.password.required"
                class="form-text text-danger"
              >This field is required!</small>
              <small
                v-if="!$v.password.numeric"
                class="form-text text-danger"
              >Your password must consist of numbers!</small>
              <small
                v-if="!$v.password.minLength && $v.password.$error"
                class="form-text text-danger"
              > Your password must contain minimum {{$v.password.$params.minLength.min}} characters!</small>
              <small
                v-if="!$v.password.maxLength && $v.password.$error"
                class="form-text text-danger"
              >Your password must contain maximum {{$v.password.$params.maxLength.max}} characters!</small>
            </div>
            <div class="form-group">
              <label for="col-sm-2 col-form-label">rePassword</label>
              <input
                @blur="$v.repassword.$touch()"
                type="password"
                class="form-control"
                :class="{'is-invalid':$v.repassword.$error,'is-valid':!$v.repassword.$invalid && password!=null}"
                v-model="repassword"
                placeholder="Re-enter your password..."
              />
              <small
                v-if="$v.repassword.$error && !$v.repassword.required"
                class="form-text text-danger"
              >This field is required!</small>
              <small
                v-if="$v.repassword.$error && !$v.repassword.sameAs"
                class="form-text text-danger"
              >The passwords you entered do not match each other!</small>
            </div>
            <div class="form-group">
              <label for="col-sm-2 col-form-label">Age</label>
              <input
                @blur="$v.age.$touch()"
                type="text"
                class="form-control"
                :class="{'is-invalid':$v.age.$error,'is-valid':!$v.age.$invalid}"
                v-model="age"
                placeholder="Enter your age..."
              />
              <small
                v-if="$v.age.$error && !$v.age.required"
                class="form-text text-danger"
              >This field is required!</small>
              <small
                v-if="!$v.age.numeric"
                class="form-text text-danger"
              >Your age must consist of numbers!</small>
              <small
                v-if="!$v.age.between && $v.age.$error"
                class="form-text text-danger"
              >Your age must be between {{$v.age.$params.between.min}} and {{$v.age.$params.between.max}}!</small>
            </div>
            <div class="form-group">
              <label for="col-form-label">The category you want to register</label>
              <select class="form-control" v-model="$v.selectedCategory.$model">
                <option :value="null" selected disabled hidden>Choose a category...</option>
                <option
                  :value="category"
                  v-for="(category) in categories"
                  :key="category"
                >{{category}}</option>
              </select>
              <small
                class="form-text text-danger"
                v-if="!$v.selectedCategory.checked"
              >You must select a category!</small>
            </div>
            <button
              class="btn btn-primary btn-sm mt-2 mb-2"
              @click.prevent="addHobby"
            >Add Interest</button>
            <div id="hobbies" class="p-2" :class="{'border':hobbies.length>0,'border-primary':hobbies.length>0}">
              <div
                class="form-group mb-1"
                v-for="(hobby,index) in hobbies"
                :key="index"
                :id="index"
              >
                <div class="input-group">
                  <input
                    ref="hobby"
                    @blur="$v.hobbies.$each[index].value.$touch()"
                    type="text"
                    class="form-control"
                    :class="{'is-invalid':$v.hobbies.$each[index].$error,'is-valid':!$v.hobbies.$each[index].$invalid}"
                    v-model="hobby.value"
                  />
                  <span class="input-group-btn">
                    <button class="btn btn-danger" @click.prevent="removeHobby">Remove</button>
                  </span>
                </div>
                <small class="form-text text-danger" v-if="$v.hobbies.$each[index].$error && !$v.hobbies.$each[index].minLength">You must enter at least {{$v.hobbies.$each[index].value.$params.minLength.min}} characters!</small>
              </div>
            </div>
            <small
              class="form-text text-danger"
              v-if="!$v.hobbies.minLength || !$v.hobbies.required"
            >You must enter at least {{$v.hobbies.$params.minLength.min}} interests!</small>
            <button class="btn btn-outline-primary btn-sm mt-3" @click.prevent :disabled='$v.$invalid'>Register</button>
          </form>
        </div>
        
      </div>
    </div>
  </div>
</template>

<script>
import {
  required,
  email,
  numeric,
  minLength,
  maxLength,
  sameAs,
  between
} from "vuelidate/lib/validators";

export default {
  name: "App",
  data() {
    return {
      email: null,
      password: null,
      repassword: null,
      age: null,
      selectedCategory: null,
      categories: [
        "Yazılım",
        "Donanım",
        "Cloud",
        "Sunucular",
        "Unix",
        "Linux",
        "Mac Os",
        "Windows"
      ],
      hobbies: [],
      validPassword: false
    };
  },
  validations: {
    email: {
      required,
      email,
      // uniq(value){
      //   return value !=="gurkanbulca@gmail.com" ? true : false;
      // },
      uniq(value){ // custom validator
        return new Promise((resolve)=>{ // simule edilmiş asenkron yapı
          setTimeout(()=>{
            resolve(value !== 'gurkanbulca@gmail.com' ? true : false)
          },1000)
        })
        // return value !== 'gurkanbulca@gmail.com' ? true : false
      }
    },
    password: {
      required,
      numeric,
      minLength: minLength(6),
      maxLength: maxLength(8)
    },
    repassword: {
      required,
      numeric,
      minLength: minLength(6),
      maxLength: maxLength(8),
      sameAs: sameAs("password")
    },
    age: {
      required,
      numeric,
      between: between(18, 60)
    },
    selectedCategory: {
      checked(val, vm) { // custom validator
        return vm.selectedCategory !== null ? true : false;
      }
    },
    hobbies: {
      required,
      minLength: minLength(2),
      $each: {
        // Arrayin her bir elemanı için validate işlemi
        value: {
          required,
          minLength: minLength(6)
        }
      }
    }
  },
  methods: {
    addHobby() {
       this.hobbies.push({ value: null })
        
      setTimeout(()=>{
        console.log();
        document.getElementById('hobbies').lastElementChild.firstElementChild.firstElementChild.focus()
      },10)
      
    },
    removeHobby(event) {
      console.log(event.target.parentElement.parentElement.parentElement.id);
      this.hobbies.splice(
        event.target.parentElement.parentElement.parentElement.id,
        1
      );
    }
  }
};
</script>

<style>
label {
  font-weight: bold;
}

#deleteButton {
  display: inline-block;
}
</style>
