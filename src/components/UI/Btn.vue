<template>
    <a href="#" class="btn">
        <slot></slot>
    </a>
</template>

<script>
    export default {
        
    }
</script>

<style scoped>
.btn {
  display: inline-block;
  color: teal;
  font-weight: 700;
  background: white;
  text-decoration: none;
  user-select: none;
  padding: .5em 2em;
  outline: none;
  border: 2px solid;
  border-radius: 1px;
  transition: 0.2s;
  border: 1px solid teal;
  
} 
.btn:hover { background: rgb(79, 230, 230) } /* при наведении курсора мышки */
.btn:active { background: teal; } /* при нажатии */
</style>