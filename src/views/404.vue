<template>
  <div>
    <div class="court">
      <img src="@/assets/images/missed-ball.svg" alt="court"/>
    </div>
    <div class="half-right">
      <h1 class="big__text">404</h1>
      <h3 class="sub__text">Casi fue donde querías. Vaya a la página de inicio y vuelva a intentarlo</h3>
      <router-link to='/' :class="['link', {visible: isVisible} ]"><button class= 'btn color'>Vamos a empezar de nuevo</button></router-link>
    </div>
  </div>
</template>

<script setup>
import {ref, onMounted} from 'vue'

onMounted( () => makeItVisible(2000) )

const isVisible = ref(false)
function makeItVisible (timeout) {
  setTimeout( () => isVisible.value = true, timeout) 
}

</script>


<style lang="scss" scoped>
  @import '@/colors.scss';
  .half-right {
    margin-left: 50%;
    padding-right: 70px;
    .link {
      float: right;
      opacity: 0;
      transition: opacity 1s ease-in-out;
      &.visible { opacity: 1 }
      .btn {
        border-radius: 8px;
      }
    }
  }

  .big__text,
  .sub__text {
    color: $white;
    text-shadow: 0 0 1px rgba($greyLight, .4);
    text-align: right;
  }
.big__text {
  font-weight: 100;
  margin: .3em 0 .4em;
}
.sub__text {
  margin: 0 0 1em;
  line-height: 1.1;
  // float: right;
  font-weight: 300;
  &:last-of-type{
    margin-bottom: 1em;
  }
}
.court{

  position: fixed;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  z-index: -100;
  
  img {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    min-width: 50%;
    min-height: 50%;
  }
  // object-fit: contain;
  // position: fixed;
  // top: 0;
  // left: 0;
  // min-width: 100%;
  // min-height: 100%;
  // // right: 0;
  // // bottom: 0;
  // z-index: -100;
}
.views {
  padding: 0;

}

@media screen and (max-height: 400px) {
  .big__text{
    margin: 1rem 0 .9rem;
  }
  .sub__text{
    margin: 2rem 0 1rem;
  }
}
@media screen and (orientation: landscape) {
  .big__text{
    font-size: clamp(2rem, 20vh, 16rem);
  }
  .sub__text{
    font-size: clamp(1.6rem, 6vh, 4rem);
  }

  .link .btn{
    font-size: clamp(1.2rem, 3vh, 4rem);
  }
  .court img{
    object-position: 0vw -10vh;
  }
}
@media screen and (orientation: portrait) {

  .big__text{
    font-size: clamp(2rem, 20vw, 16rem);
  }
  .sub__text{
    font-size: clamp(1.6rem, 6vw, 4rem);
  }
  .link .btn{
    font-size: clamp(1.2rem, 3vw, 4rem);
  }
  .court img{
    object-position: -10vw 0vh;
  }
}
@media screen and (max-height: 481px) and (orientation: landscape) {
  .court img{
    object-position: 0vw -30vh;
  }
}

@media screen and (max-width: 481px) and (orientation: portrait) {
  .court img{
    object-position: -60vw 0vh;
  }
}
</style>