<template>
    <div class="wrapper">
        <v-head></v-head>
        <v-sidebar></v-sidebar>
        <div class="content-box"
             :class="{'content-collapse':collapse}">
            <div class="content">
                <transition name="move" mode="out-in">
                    <keep-alive :include="tagsList">
                        <router-view></router-view>
                    </keep-alive>
                </transition>
            </div>
        </div>
    </div>
</template>

<script>
    import vHead from './Header.vue';
    import vSidebar from './Sidebar.vue';
    export default {
        data(){
            return {
                tagsList: [],
                collapse: true
            }
        },
        components:{
            vHead, vSidebar
        },
        created(){
            this.$bus.$on('collapse', msg => {
                this.collapse = msg;
            });

            // 只有在标签页列表里的页面才使用keep-alive，即关闭标签之后就不保存到内存中了。
            this.$bus.$on('tags', msg => {
                let arr = [];
                for(let i = 0, len = msg.length; i < len; i ++){
                    msg[i].name && arr.push(msg[i].name);
                }
                this.tagsList = arr;
            });
            let user = localStorage.getItem('user');
            if (user){
                if (typeof user =='string'){
                    user = JSON.parse(user);
                }
                this.$store.commit('UPDATE_SLIDES', user.limits);
                this.$router.replace(user['home']);
            }
        }
    }
</script>
