<template>
  <div class="footerConteiner">
    <div class="contactConteiner">
      <div class="fTitle">Contacts:</div>
      <div class="contactsInfo">
        <div class="contact phone"><span class="glyphicon glyphicon-earphone"></span>&nbsp; 0883 326 561</div>
        <div class="contact email"><span class="glyphicon glyphicon-envelope"></span>&nbsp; nikolai.dikov@yahoo.com</div>
        <div class="contact git"><span class="glyphicon glyphicon-book"></span>&nbsp; <a href="https://github.com/NikolayDikov" target="_blank">GitHub</a></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Footer",
};
</script>

<style scoped>
a, a:link, a:visited {
  color: white;
  background-color: transparent;
  text-decoration: none;
}
a:hover {
  color: rgb(252, 184, 19);
  text-decoration: none;
}
.contact {
    display: flex;
}
.fTitle {
    display: flex;
    justify-content: center;
    align-self: center;
    color: rgb(255, 255, 255);
    font-weight: 700;
    font-size: 1.5em;
    margin-bottom: 10px;
}
.contactsInfo {
    width: 50%;
    display: flex;
    flex-direction: row;
    align-self: center;
    color: rgb(255, 255, 255);
    border-top: 1px solid white;
    margin-bottom: 10px;
    padding-top: 5px;
    justify-content: space-between;
}
.contactConteiner {
    justify-content: left;
    width: 80%;
    background-color: rgba(0, 34, 114, 0.900);
    margin: auto;
    display: flex;
    flex-direction: column;
}
.footerConteiner {
  background-image: url("../assets/layoutback.jpg");
  background-repeat: no-repeat;
  border-top: 5px solid rgba(0, 34, 114, 1);
  background-position: center;
  display: flex;
  justify-content: center;
  flex-direction: column;
}
/* Mobile changes */
@media screen and (max-width: 900px) {
    .contactsInfo {
        width: 100%;
        flex-direction: column;
        justify-content: center;
    }
    .contact {
        justify-content: center;
    }
}
</style>