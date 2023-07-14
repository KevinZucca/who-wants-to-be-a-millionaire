<script>


export default {
    name: 'MenuPanel',
  data() {
    return {
      
  }
},

 components: {
}
 }


</script>

<template>

<div class="container">
    <div id="menu">
        <ul>
            <li>
                <router-link class="nav-link" exact-active-class="active" :to="{ name: 'game-page'}"><p>Gioca!</p></router-link>
            </li>
            <li>
                <p>Classifiche</p>
            </li>
            <li>
                <p>Impostazioni</p>
            </li>
        </ul>

    </div>
</div>


</template>

<style lang="scss" scoped>

.container {
    height: 600px;
    color: white;
    background-color: #11093a;
    position: relative;

    display: flex;
    justify-content: center;
    align-items: center;

    #menu {
        width: 50%;
        background-color: #ffffff46;
        height: 80%;

        border-radius: 5%;

        ul {
            display: flex;
            justify-content: center;
            align-items: center;
            flex-flow: column;

            list-style-type: none;
            padding-left: 0;
            
            li {
                border-bottom: 1px solid rgba(0, 0, 0, 0.288);
                font-size: 1.5em;

                p {
                    text-decoration: none;
                    color: white;
                }
            }
        }
    }
}
</style>
