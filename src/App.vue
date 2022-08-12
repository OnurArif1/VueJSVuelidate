<template>
  <div class="container">
    <h3 class="text-center mt-3">Vuelidate ile Form Kontrolü</h3>
    <div class="d-flex justify-content-center align-content-center flex-row">
      <div class="card p-4 mt-3  shadow">
        <form style="width: 350px" @submit.prevent="onSubmit">
          <div class="form-group">
            <label>E-posta Adresiniz</label>
            <input 
              @blur="$v.email.$touch()"
              v-model="email" 
              type="email" 
              class="form-control" 
              :class="{'!is-invalid' : $v.email.$eror}"
              placeholder="E-posta adresini giriniz">
              <!--:class="{'!is-invalid' : $v.email.$eror} burada hata olup olmadığında göre borderı değişme-->
              <!--@blur; içinde emaili kontrol et dedik-->
            <small v-if="!$v.email.required" class="form-text text-danger">Bu alanı doldurmak zorunludur </small>
            <small v-if="!$v.email.email" class="form-text text-danger">Lütffen geçerli posta adersi girin</small>
          </div>
          <div class="form-group">
            <label>Şifre</label>
            <input 
              v-model="$v.password.$model" 
              type="password" 
              class="form-control" 
              placeholder="Şifrenizi giriniz">
            <small v-if="!$v.password.required" class="form-text text-danger">Bu alanı doldurmak zorunludur </small>
            <small v-if="!$v.password.numeric" class="form-text text-danger">Şifre rakamlardan oluşsun </small>
            <small v-if="!$v.password.minLength" class="form-text text-danger">Şifre {{ $v.password.$params.minLength.min }} rakamdan oluşmalı</small>
            <small v-if="!$v.password.maxLength" class="form-text text-danger">Şifre {{ $v.password.$params.maxLength.max }} rakamdan oluşmalı</small>
          </div>
          <div class="form-group">
            <label>Şifre Tekrar</label>
            <input 
              v-model="$v.repassword.$model" 
              type="password" 
              class="form-control" 
              placeholder="Şifrenizi tekrar giriniz">
            <small v-if="!$v.repassword.required" class="form-text text-danger">Bu alanı doldurmak zorunludur </small>
            <small v-if="!$v.repassword.numeric" class="form-text text-danger">Şifre rakamlardan oluşsun </small>
            <small v-if="!$v.repassword.minLength" class="form-text text-danger">Şifre {{ $v.password.$params.minLength.min }} rakamdan oluşmalı</small>
            <small v-if="!$v.repassword.maxLength" class="form-text text-danger">Şifre {{ $v.password.$params.maxLength.max }} rakamdan oluşmalı</small>
            <small v-if="!$v.repassword.sameAs" class="form-text text-danger">Girdiğimiz şifre birbiri ile uyuşmadı</small>
          </div>
          <div class="form-group">
            <label>Yaşınızı girin</label>
            <input 
              v-model="$v.age.$model" 
              type="text" 
              class="form-control" 
              placeholder="Yaşınızı giriniz">
            <small v-if="!$v.age.required" class="form-text text-danger">Bu alanı doldurmak zorunludur </small>
            <small v-if="!$v.age.numeric" class="form-text text-danger">Sadece rakam giriniz</small>
            <small v-if="!$v.age.between" class="form-text text-danger">
              Kayıt olmak için yaşınızın {{ $v.age.$params.between.min }} ile {{ $v.age.$params.between.max }} arasında olmalı
            </small>
          </div>
          <div class="form-group">
            <label>Kayıt olmak istediğiniz kategori</label>
            <select v-model="selectedCategory" class="form-control">
              <option v-for="category in categories" :value="category" :key="category">{{ category }}</option>
            </select>
          </div>
          <a @click="newHobby" class="text-white btn btn-secondary rounded-0 btn-sm">İlgi Alanı Ekle</a>
          <ul class="list-group mt-3 mb-3 border-0">
            <li v-for="(hobby,index) in hobbies" :key="hobby" class="list-group-item d-flex pl-2">
              <input type="text" class="form-control mr-2" v-model="hobby.value">
              <button class="btn btn-sm btn-danger rounded-0" @click="hobbies.splice(index, 1)">Sil</button>
            </li>
          </ul>
          <button class="btn btn-outline-secondary rounded-0">Kaydet</button>
        </form>
      </div>
      <div class="card p-4 mt-3 mr-4 shadow" style="width:400px">
        <p>{{ $v.repassword }}</p>
      </div>
    </div>
  </div>
</template>
<script>
import {required,email,numeric,minLength,maxLength,sameAs,between} from "vuelidate/lib/validators"
  export default {
    data() {
      return {
        email : null,
        password : null,
        repassword : null,
        selectedCategory : null,
        age:null,
        categories : ["Yazılım", "Donanım", "Cloud", "Sunucular", "Unix", "Linux", "Mac OS", "Windows"],
        hobbies: []
      }
    },
    validations:{
      email:{
        required, //required: formu göndermeden önce bir girşi alanının doldurulması gerektiğini belirtir
        email
      },
      password:{
        required,
        numeric,
        minLength:minLength(6),
        maxLength:maxLength(8)
      },
      repassword:{
        required,
        numeric,
        minLength:minLength(6),
        maxLength:maxLength(8),
        // sameAs:sameAs('password') //burada password de ggirdiğimiz şeuleri repassword de girdiğimiz şeylerle karşılaştırdık
        sameAs:sameAs(vm=>{
          return vm.password + '20'; //burada şifreyi girdiğinde sonuna 20 eklemiş mş diye kontrol ettik
        })
      },
      age:{
        required,
        numeric,
        between:between(18,60) //girilen yaşı burada kontrol edildi
      }
    },
    methods: {
      onSubmit(){
        let form = {
          email : this.email,
          password : this.password,
          category : this.category,
          hobbies : this.hobbies,
        }
        console.log(form)
      },
      newHobby(){
        let hobby = {
          id: Math.random() * Math.random() * 1000,
          value: ''
        }
        this.hobbies.push(hobby)
      }
    }
  }
</script>
