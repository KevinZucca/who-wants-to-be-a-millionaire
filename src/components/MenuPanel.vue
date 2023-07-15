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
            <router-link class="nav-link" exact-active-class="active" :to="{ name: 'game-page'}">
                <li>
                    <p>Gioca!</p>
                </li>
            </router-link>
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
        width: 30%;
        height: 80%;

        ul {
            display: flex;
            justify-content: center;
            align-items: center;
            flex-flow: column;
            gap: 1.5em;

            list-style-type: none;
            padding-left: 0;

            .nav-link {
                    text-decoration: none;
                }
            
            li {
                width: 400px;
                height: 60px;

                border: 1px solid rgba(255, 255, 255, 0.753);
                background-color: #1f1747;
                border-radius: 20px;
                font-size: 1.5em;

                display: flex;
                justify-content: center;
                align-items: center;
                transition: all .2s ease-in-out;

                &:hover {
                    scale: 1.1;
                    cursor: pointer;
                    background-color: #393358;
                }

                p {
                    text-decoration: none;
                    color: white;
                }
            }
        }
    }
}
</style>
