<script>

export default {
    name: 'MenuPanel',
  data() {
    return {
      difficulty: 'easy',
  }
},

 methods: {

 },

 components: {
}
 }


</script>

<template>

<div class="my-container">
    <div id="menu">

        <h3>Select difficulty</h3>
        <select class="selection" v-model="this.difficulty">
            <option value="easy">Easy</option>
            <option value="medium">Medium</option>
            <option value="hard">Hard</option>
        </select>

        <router-link class="menu-link selection" exact-active-class="active" :to="{ name: 'game-page', query: { difficulty: difficulty }}">
            <p>Play!</p>
        </router-link>
        
    </div>
</div>


</template>

<style lang="scss" scoped>

.my-container {
    height: calc(100vh - 300px);
    width: 100%;
    
    color: white;
    background-color: #11093a;
    position: relative;

    display: flex;
    justify-content: center;
    align-items: center;

    #menu {
        width: 30%;
        height: 100%;

        padding-top: 50px;

        display: flex;
        align-items: center;
        flex-flow: column;
        gap: 1.5em;

        h3 {
            margin-bottom: 0;
        }

        .selection {
            border: 1px solid rgba(255, 255, 255, 0.753);
            background-color: #1f1747;
            border-radius: 20px;
            font-size: 1.5em;
            color: white;
            text-align: center;

            display: flex;
            justify-content: center;
            align-items: center;
            transition: all .2s ease-in-out;
        }

        .menu-link {
            width: 400px;
            height: 60px;
            text-decoration: none;

            &:hover {
                scale: 1.1;
                cursor: pointer;
                background-color: #090b8c82;
            }
            
            p {
                text-decoration: none;
                color: white;
                margin-bottom: 4px;
            }
        }


        select {
            width: 150px;
            height: 50px;

            option {
                padding: 10px;
            }

            &:hover {
                cursor: pointer;
            }

        }
    }
}

@media only screen and (max-width: 991px){
    .my-container {
        #menu {
            h3 {
                min-width: 180px;
            }

            .selection {
                width: 120px;
                font-size: 1.1em;
            }

        }
    }
}
</style>
