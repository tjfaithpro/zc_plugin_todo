<template>
    <div
        class="collab-dialog td-absolute td-p-5 td-bg-white td-rounded td-shadow td-border td-mt-12 td-top-0 td-right-0"
    >
        <h3><b>All Collaborators</b></h3>
        <small class="td-text-gray-500">
            Assign new collaborators for this task. You can also remove those
            that are no longer needed</small
        >

        <!-- COLLABORATOR TAB MENU -->
        <div class="td-mt-8 td-mb-6 td-grid td-grid-cols-2 td-flex-row td-items-stretch td-justify-items-stretch">
        <div
            class="td-p-2 td-text-center "
            :class="{ active: isActive1 }"
            @click="switchTab(true)"
        >
            <b>Currnet Collaborators</b>
        </div>
        <div class=" td-text-center td-p-2 " :class="{ active: isActive2 }" @click="switchTab(false)">
            <b>All Users</b>
        </div>
        </div>

        <!-- SPINNER -->
        <div v-if="showLoading" class="td-m-auto td-flex td-justify-center td-items-center">
           
            <div  class="lds-ellipsis">
                <div></div>
                <div></div>
                <div></div>
                <div></div>
            </div>
            <small class="td-text-gray-500">Loading users...</small>
            
        </div>
        
        <!-- COLLABORATOR TAB CONTENT -->
        <div v-else>
        <!-- current collaborators -->
        <div  v-if="activeCollabTab" class="td-h-64 td-overflow-y-scroll" >
            <!-- Admin Collaborators  -->
           
             <div>
            <div class="td-px-3 td-flex td-justify-between td-items-center">
                <div class="td-flex td-items-center ">
                    <img class="td-w-10 td-mr-2 td-h-10 td-rounded td-border-2 td-border-green-500 " src="https://picsum.photos/200/300"/>
                    <div>
                        <div class="td-text-green-500">
                            <span><b>Charles Jekkins</b></span>
                            <i class="pi pi-user td-px-1" />
                        </div>
                        <small>
                        Senior Developer | Admin

                        </small>
                    </div>
                </div>
            <div>
                
                <button class="td-border-green-500 td-border-2 td-mr-3 td-bg-green-500 td-hover:bg-green-700 td-text-white td-py-2 td-px-4 td-rounded">Revoke Admin</button>
                <button class="td-border-green-500 td-border-2 td-hover:bg-green-700 td-text-green-500 td-py-2 td-px-4 td-rounded"><u>Remove</u></button>
            </div>
            </div>
                <hr class="td-my-3">
            </div>
           <!-- General Collaborators -->
        <div>
            <div class="td-px-3 td-flex td-justify-between td-items-center">
                <div class="td-flex td-items-center ">
                    <img class="td-w-10 td-mr-2 td-h-10 td-rounded " src="https://picsum.photos/200/300"/>
                    <div>
                        <div>
                            <span><b>Charles Jekkins</b></span>
                        </div>
                        <small>
                        Senior Developer

                        </small>
                    </div>
                </div>
            <div>
                
                <button class="td-border-green-500 td-border-2 td-mr-3 td-bg-green-500 td-hover:bg-green-700 td-text-white td-py-2 td-px-4 td-rounded">Make Admin</button>
                <button class="td-border-green-500 td-border-2 td-hover:bg-green-700 td-text-green-500 td-py-2 td-px-4 td-rounded"><u>Remove</u></button>
            </div>
            </div>
                <hr class="td-my-3">
        </div>
            
        </div>
        <!-- all users -->
        <div v-else class="td-h-64 td-overflow-y-scroll">
           <!-- All Users -->
        <div :for="user.user_name" v-for="(user, index) in users" :key="index">
            <div class="td-px-3 td-flex td-justify-between td-items-center">
                <div class="td-flex td-items-center ">
                    <img class="td-w-10 td-mr-2 td-h-10 td-rounded " src="https://picsum.photos/200/300"/>
                    <div>
                        <div>
                            <span><b>Charles Jekkins</b></span>
                        </div>
                        <small>
                        Senior Developer

                        </small>
                    </div>
                </div>
            <div v-if="!user.collaborator || user.collaborator == false">
                
                <button @click="toggleCollab(user, index, 'admin')" class="td-border-green-500 td-border-2 td-mr-3 td-bg-green-500 td-hover:bg-green-700 td-text-white td-py-2 td-px-4 td-rounded">Make Admin</button>
                <button  @click="toggleCollab(user, index, true)" class="td-border-green-500 td-border-2 td-hover:bg-green-700 td-text-green-500 td-py-2 td-px-4 td-rounded"><u>Add user</u></button>
            </div>
             <div v-else-if="user.collaborator == true">
               <small class="td-text-gray-500">Already a collaborator in current todo</small>
            </div>
            </div>
                <hr class="td-my-3">
        </div>
        </div>
        </div>
    </div>
</template>
<script>
import axios from "axios";
import { mapGetters } from "vuex";
import { mapActions } from "vuex";
export default {
    name: "TodoCollaborators",
    data() {
        return {
            activeCollabTab: true,
            isActive1: true,
            isActive2: false,
            showLoading:false,
            users: [],
        };
    },
    computed: {
        ...mapGetters({
             isUser: 'todos/user',
        })
    },
    components: {},
    methods: {
        ...mapActions({
      selectedTodo: 'todos/selectedTodo',
      totalCollab: 'todo/totalCollaborator'
    }),
        switchTab(value) {
            this.activeCollabTab = value;
            this.isActive1 = !this.isActive1;
            this.isActive2 = !this.isActive2;
        },

        // COLLABORATOR CODE START HERE ==============BY TJ FAITH
            async getUser() {
                
                 this.showLoading = true
            await axios.get(`https://api.zuri.chat/organizations/${this.isUser.currentWorkspace}/members`)
                // .then(response =>  this.users = (response.data.results))
                .then((response)=>{
                    this.showLoading = false
                    this.users = response.data.data
                    this.totalUsers = response.data.data
                    this.users.forEach((element,index) => {
                        this.selectedTodo.collaborators.forEach(collab=>{
                            if(element._id == collab.collaborator_id){
                                this.users[index].collaborator = true
                            }
                        })
                    });
                        console.log(this.users)

                    // console.log(response.data.data)
                })

            .catch((error)=>{
                    this.showLoading = false
                console.log(error)
                snack.danger({
                    position: 'bottom-right',
                    text: `Test Danger ${Date.now()}`,
                    button: 'ACTION',
                    time: 2000,
                    close: false,
                    action: () => { console.log('do something'); },
                });
            })
  },

//   THIS FUNCTON COUNT COLLABORATOR
            countCollaborator(){
            this.totalCollab = this.selectedTodo.collaborators.length;
            },

             countCollaboratorAdmin(){
                 let counter = 0;
                 this.selectedTodo.collaborators.forEach((element, index) => {
                     if (this.selectedTodo.collaborators[index].admin_status == '1'){
                         counter++;
                     }
                 });
             this.totalCollabAdmin = counter;
            },

            toggleCollab(user, index, value){
                this.isAssign =false
                if (value == true){
                  let data={
                    admin_status:'0',
                    collaborator_id:user._id,
                    user_id:this.isUser["0"]._id,
                    email:user.email,
                    name:user.user_name
                 }
                 console.log(this.selectedTodo)
                 axios.put(`https://todo.zuri.chat/api/v1/assign-collaborators/${this.selectedTodo._id}?organisation_id=${this.selectedTodo.organisation_id}`, data).then((request)=>{
                console.log(request)
                if(request.data.status =="success"){
                    alert('Collaborator Added')
                    // Update Selected Todo
                    this.selectedTodo.collaborators.push(data)
                     this.users[index].collaborator = value
                    this.countCollaborator();
                    this.$emit("totalCollab", this.countCollaborator());
                }else{
                    alert('Oops..an error occured')
                }
            this.adding =false
    
            }).catch((error)=>{
                    alert('Oops..an error occured')
               
                console.log(error)
            this.adding =false

            })
                } else if(value == false){
                    user.collaborator = value
                   let index = this.selectedTodo.collaborators.findIndex(x => x.collaborator_id === user._id);
                    this.selectedTodo.collaborators.splice(index, 1)
                    this.countCollaborator();

                } else if (value == 'admin'){
                    // SET A COLLABORATOR AS ADMIN
                    // CALL YOUR API HERE TO ADD ADMIN TO DATABASE =================================
                //    API
                //     API
                   
                   
                   
                   let index = this.selectedTodo.collaborators.findIndex(x => x.collaborator_id === user._id);
                    this.selectedTodo.collaborators[index].admin_status = value
                    this.countCollaboratorAdmin();

                }
                // alert(value)
            },

            search(){
                    let value;
            if (this.value != "") {
                value = this.totalUsers;
                this.users = value
                value = this.users.filter(
                    user =>
                        user.user_name
                            .toLowerCase()
                            .indexOf(this.value.toLowerCase()) >= 0
                );
                 this.users = value 
            } else {
                this.users = this.totalUsers;
                console.log(this.users)
                // this.users = value;
                // alert(value)
            }

            this.searchValue = value;
            },
            // COLLABORATOR CODE END HERE =================================BY TJFAITH
    },
    mounted() {
        this.selectedTodo(this.$route.params.id)
        this.getUser()
    },
    beforeMount() {}
};
</script>
<style lang="scss" scoped>
.collab-dialog {
    width: 35rem;
}
.active {
    background-color: #e1fdf4;
    border-bottom: 4px solid #14835f;
}

// custom scrollbar
/* width */
::-webkit-scrollbar {
  width: 8px;
//   display: none;
}

/* Track */
::-webkit-scrollbar-track {
//   box-shadow: inset 0 0 5px grey; 
//   border-radius: 10px;
}
 
/* Handle */
::-webkit-scrollbar-thumb {
  background: #14836079; 
  border-radius: 10px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #14835f; 
}


// CUSTOM SPINNER
.lds-ellipsis {
  display: inline-block;
  position: relative;
  width: 80px;
  height: 80px;
}
.lds-ellipsis div {
  position: absolute;
  top: 33px;
  width: 13px;
  height: 13px;
  border-radius: 50%;
  background: #14835f;
  animation-timing-function: cubic-bezier(0, 1, 1, 0);
}
.lds-ellipsis div:nth-child(1) {
  left: 8px;
  animation: lds-ellipsis1 0.6s infinite;
}
.lds-ellipsis div:nth-child(2) {
  left: 8px;
  animation: lds-ellipsis2 0.6s infinite;
}
.lds-ellipsis div:nth-child(3) {
  left: 32px;
  animation: lds-ellipsis2 0.6s infinite;
}
.lds-ellipsis div:nth-child(4) {
  left: 56px;
  animation: lds-ellipsis3 0.6s infinite;
}
@keyframes lds-ellipsis1 {
  0% {
    transform: scale(0);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes lds-ellipsis3 {
  0% {
    transform: scale(1);
  }
  100% {
    transform: scale(0);
  }
}
@keyframes lds-ellipsis2 {
  0% {
    transform: translate(0, 0);
  }
  100% {
    transform: translate(24px, 0);
  }
}

</style>
