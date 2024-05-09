<template>
    <div class="modal">
      <div class="modal-content">
        <span class="close" @click="closeIntro">&times;</span>
        <div class="text">
            <h1>Welcome to Color Match!</h1>
            <p>Match the color a piece of text says to the box of that color.</p>
            <p>Each round the time to match them all will decrease. Better be quick!</p>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    methods: {
      closeIntro() {
        this.$emit('close');
      }
    },
  }
  </script>
  
  <style scoped>
    @import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600;700&display=swap');

  .modal {
    display: block;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgba(0,0,0,0.5);
    font-family: 'Open Sans', sans-serif;
    transition: opacity 1.0s ease;
  }

  .modal.fade-out {
  opacity: 0;
  pointer-events: none; /* Disable pointer events to make the modal not clickable during the fade-out */
  }

  h1, p {
    display: flex;
    justify-content: center;
  }
  
  .modal-content {
    background-color: #fefefe;
    margin: 5% auto;
    padding: 20px;
    border: 1px solid #888;
    width: 40%;
    border-radius: 10px;
  }
  
  .close {
    color: #aaa;
    float: right;
    font-size: 28px;
    font-weight: bold;
  }
  
  .close:hover,
  .close:focus {
    color: black;
    text-decoration: none;
    cursor: pointer;
  }
  </style>