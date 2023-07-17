<script>

export default {
    name: 'AppHeader',
  data() {
    return {
  }
},
}


</script>

<template>

<header>
    <div id="logo-container">
        <img src="https://boolean.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0b8e1102-7ff4-4870-a39e-ef625253af75%2Flogo.png?id=d1419994-97c4-4690-b349-03db5eada926&table=block&spaceId=4c49ff97-016b-4669-8de5-4479dd1a86e1&width=480&userId=&cache=v2" alt="logo-img">
    </div>

</header>

</template>

<style lang="scss" scoped>

header {
        height: 300px;
        background-color: #090b8c;
        position: relative;

        display: flex;
        justify-content: center;
        align-items: center;

    }

    #logo-container {
        margin: 0 auto;

        width: 250px;
        height: 250px;
        
        display: flex;
        justify-content: center;
        align-items: center;

        img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
    }
</style>
