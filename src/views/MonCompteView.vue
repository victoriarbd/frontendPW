<template>
  <h2>Mon compte</h2>
  <div class="container-fluid">
    <div class="row row-cols-5 row-cols-lg-5 g-2 g-lg-2">
        <div class="card mb-3" v-if="!this.modifier">
          <div class="card-body">
            <div class="row">
              <div class="col-sm-3">
                <h6 class="mb-0">Nom</h6>
              </div>
              <div class="col-sm-9 text-secondary">
                {{ oneUser.nom }}
              </div>
            </div>
            <hr />
            <div class="row">
              <div class="col-sm-3">
                <h6 class="mb-0">Prénom</h6>
              </div>
              <div class="col-sm-9 text-secondary">
                {{ oneUser.prenom }}
              </div>
            </div>
            <hr />
            <div class="row">
              <div class="col-sm-3">
                <h6 class="mb-0">Email</h6>
              </div>
              <div class="col-sm-9 text-secondary">
                {{ oneUser.email }}
              </div>
            </div>

            <hr />
            <div class="row">
              <div class="col-sm-12">
                <div class="form-check form-switch">
                  <input
                    class="form-check-input"
                    type="checkbox"
                    role="switch"
                    v-model="update"
                    id="flexSwitchCheckDefault"
                    @click="modifierProfil"
                  />
                  <label class="form-check-label" for="flexSwitchCheckDefault">
                    Modifier votre profil</label
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      
      
        <div class="card" v-if="this.modifier">
          <div class="card-body">
            <div class="row mb-3">
              <div class="col-sm-3">
                <h6 class="mb-0">Nom</h6>
              </div>
              <div class="col-sm-9 text-secondary">
                <input type="text" class="form-control" v-model="oneUser.nom" />
              </div>
            </div>
            <div class="row mb-3">
              <div class="col-sm-3">
                <h6 class="mb-0">Prénom</h6>
              </div>
              <div class="col-sm-9 text-secondary">
                <input
                  type="text"
                  class="form-control"
                  v-model="oneUser.prenom"
                />
              </div>
            </div>
            <div class="row mb-3">
              <div class="col-sm-3">
                <h6 class="mb-0">Email</h6>
              </div>
              <div class="col-sm-9 text-secondary">
                <input
                  type="email"
                  class="form-control"
                  v-model="oneUser.email"
                />
              </div>
            </div>
            <div class="row mb-3">
              <div class="col-sm-3">
                <h6 class="mb-0">Mot de passe</h6>
              </div>
              <div class="col-sm-9 text-secondary">
                <input
                  type="password"
                  class="form-control"
                  v-model="password" placeholder=" Mot de passe"
                />
              </div>
            </div>
            <div class="row mb-3">
              <div class="col-sm-3">
                <h6 class="mb-0">Confirmer le mot de passe</h6>
              </div>
              <div class="col-sm-9 text-secondary">
                <input
                  type="password"
                  class="form-control"
                  v-model="confirmPassword" placeholder="Veuillez confirmer le mot de passe"
                />
              </div>
            </div>

            <div class="row">
              <div class="col-sm-3"></div>
              <div class="col-sm-9 text-secondary">
                <button
                  @click="updateFormUser()"
                  :class="{ disabled: !validatedFields }"
                  class="btn btn-primary"
                  type="submit"
                >
                Sauvegarder 
                </button>
                <div class="form-check form-switch">
                  <input
                    class="form-check-input"
                    type="checkbox"
                    role="switch"
                    v-model="update"
                    id="flexSwitchCheckDefault"
                    @click="visualiserProfil"
                  />
                  <label class="form-check-label" for="flexSwitchCheckDefault">
                    Visualiser votre profil</label
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'
import SHA256 from '../security/save'
export default {
  name: 'MonCompteView',
  data(){
    return {
       modifier :false,
       user: {},
       oneUser : null,
       password: '',
       confirmPassword:''
    }
  },
  beforeMount: function () {
    this.user = JSON.parse(localStorage.getItem("user"));
    console.log(this.user)
    axios.get('https://bookstore-depository-2.herokuapp.com/user/'+this.user.iduser)
      .then(response => {
        this.oneUser = response.data
        console.log("alors",response.data)
      })
      .catch(error => {
        console.log(error)
      })
      if (!localStorage.getItem('user')){
        this.$router.push('/login')
      }


  },
  computed:{
    validatedFields: function(){
      if (
        this.password != '' &&
        this.confirmPassword != ''
      ){
        return true
      }else{
        return false
      }
    }
  },
  methods:{
    modifierProfil(){
      this.modifier = true
    },
    visualiserProfil(){
      this.modifier = false
    },
    async updateFormUser(){
        if (this.password == this.confirmPassword){
          if(confirm("Do you really want to update?")){
              var data = JSON.stringify({
                nom: this.oneUser.nom,
                prenom : this.oneUser.prenom,
                email : this.oneUser.email,
                password : SHA256(this.password),
                isAdmin : this.oneUser.isAdmin,
              });
              console.log(data)
              console.log(this.user.iduser)
              var config = {
                method: 'put',
                url: 'https://bookstore-depository-2.herokuapp.com/user/'+this.user.iduser,
                headers: {
                  'Authorization': `Bearer ${this.user.token}`,
                  'Content-Type': 'application/json'
                },
                data : data
              };

              const result = await axios(config)
              if (result.status == 200){
                localStorage.removeItem("user")
                localStorage.removeItem("token")
                this.$router.push("/login")
                this.user = null
              } else {
              alert("Impossible de modifier vos informations.")
              }
            }
        }else{
          alert("Les deux mots de passe ne sont pas identiques.")
        }
            
    }
  }

}
</script>

<style scooped>
.card {
  width: 420px;
  position: relative;
  display: flex;
  flex-direction: column;
  min-width: 0;
  word-wrap: break-word;
  background-color: #fff;
  background-clip: border-box;
  border: 0 solid transparent;
  border-radius: 0.25rem;
  margin-bottom: 1.5rem;
  box-shadow: 0 2px 6px 0 rgb(218 218 253 / 65%),
    0 2px 6px 0 rgb(206 206 238 / 54%);
}
</style>
