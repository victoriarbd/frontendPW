<template>
<h1> Dépose une annonce ! </h1>
 
<BookSell/>
</template>

<script>
import BookSell from '@/components/BookSell.vue'

export default {
  name: 'BookSellView',
  components: {
    BookSell,
  },
  data(){
    return {
      categories: null,
      loading: true,
      errored: false
      //categories : [{}]
    }
  }
}

</script>

<style scoped>

.form-group {
    width:500px;
}
form{
    margin: auto; 
    float: none; 
    margin-bottom: 50px; 
    margin-top: 10px;
    padding: 25px; 
    width: 50vw;
}

button {
  margin: 20px;
}
h1{
  margin-top:10px;
}
</style>