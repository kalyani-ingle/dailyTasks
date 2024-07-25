<script>
  import { onMount } from "svelte";
  import { bind } from "svelte-simple-modal";
  import { writable } from "svelte/store";
  import Modal from "svelte-simple-modal";
  import Popup from "./Popup.svelte";

  const modal = writable(null);
  const showModal = (component, props) => modal.set(bind(component, props));
  let input;
  let ul;
  let itemsArray = localStorage.getItem("item")
    ? JSON.parse(localStorage.getItem("item"))
    : [];

  function addTask(text, timestamp) {
    let p = document.createElement("p");
    p.classList.add("date");
    p.innerHTML = new Date(timestamp).toLocaleString();

    let div = document.createElement("div");
    div.classList.add("task-item");

    let li = document.createElement("li");
    li.textContent = text;

    let updateBtn = document.createElement("button");
    updateBtn.type = "Button";
    // updateBtn.innerHTML = "Edit";
    updateBtn.innerHTML = "✏️";
    updateBtn.style.backgroundColor = "transparent";
    updateBtn.style.border = "none";
    updateBtn.style.fontSize = "large";
    updateBtn.style.width = "24px";

    let deleteBtn = document.createElement("button");
    deleteBtn.type = "Button";
    // deleteBtn.innerHTML = "Delete";
    deleteBtn.innerHTML = "❌ ";
    deleteBtn.style.backgroundColor = "transparent";
    deleteBtn.style.border = "none";
    deleteBtn.style.fontSize = "large";
    deleteBtn.style.width = "24px";

    updateBtn.addEventListener("click", function () {
      showModal(Popup, {
        text,
        onSave: (newText) => updateTask(text, timestamp, newText),
      });
    });

    deleteBtn.addEventListener("click", function () {
      removeTask(text, timestamp);
    });

    ul.appendChild(div);
    div.appendChild(li);
    div.appendChild(updateBtn);
    div.appendChild(deleteBtn);
    div.appendChild(p);
  }

  function add() {
    if (input && input.value.trim() !== "") {
      let task = {
        text: input.value,
        timestamp: Date.now(),
      };
      addTask(task.text, task.timestamp);
      itemsArray.push(task);
      localStorage.setItem("item", JSON.stringify(itemsArray));
      input.value = "";
    }
  }

  function del() {
    localStorage.clear();
    itemsArray = [];
    ul.innerHTML = "";
    
  }

  function removeTask(text, timestamp) {
    const index = itemsArray.findIndex(
      (t) => t.text === text && t.timestamp === timestamp
    );
    if (index !== -1) {
      itemsArray.splice(index, 1);
      localStorage.setItem("item", JSON.stringify(itemsArray));
      renderTasks();
    }
  }

  function updateTask(oldText, timestamp, newText) {
    if (newText !== null) {
      const index = itemsArray.findIndex(
        (t) => t.text === oldText && t.timestamp === timestamp
      );
      if (index !== -1) {
        itemsArray[index].text = newText;
        localStorage.setItem("item", JSON.stringify(itemsArray));
        renderTasks();
      }
    }
  }

  onMount(() => {
    input = document.getElementById("input");
    ul = document.getElementsByTagName("ul")[0];
    renderTasks();
  });

  function renderTasks() {
    ul.innerHTML = "";
    itemsArray.forEach((task) => addTask(task.text, task.timestamp));
  }
</script>

<main>
  <p id="notification">  </p>
  <h1>
    🚀 <br/>
    Welcome to Daily Tasks!
  </h1>
  <div id="inputContainer">
    <input
      bind:this={input}
      type="text"
      id="input"
      placeholder="Enter tasks. . ."
    />
    <button on:click={add}>Add <small>➕</small> </button>
    <button on:click={del}>Clear <small>🗑️</small></button>
  </div>
  <div id="tasksContainer">
    <p>
      Your tasks are displayed here
      <br />
      <br />
      ⬇️
    </p>
    <ul bind:this={ul}></ul>

    <Modal show={$modal} />
  </div>
</main>

<style>
  ::selection {
    background-color: rgb(255, 239, 239);
  }

  h1 {
    color: rgb(243, 202, 82);
    text-transform: uppercase;
    font-weight: 300;
  }

  main {
    text-align: center;
    padding-bottom: 30px;
    margin: 0 auto;
    /* background-color: rgb(246, 245, 242); */
    height: 100vh;
  }

  div p {
    color: rgb(108, 105, 105);
  }

  #inputContainer {
    margin-bottom: 10px;
    height: 80px;
    padding: 10px 0;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  input {
    height: 45px;
    padding: 5px;
    width: 500px;
  }

  button {
    width: 70px;
    height: 45px;
    background-color: rgb(240, 235, 227);
  }

  button:hover {
    background-color: rgb(237, 207, 234);
  }

  #tasksContainer {
    background-color: rgb(218, 209, 209);
    margin: 0 3em;
    padding: 1em 0;
  }

  ul {
    color: rgb(237, 26, 170);
    list-style: none;
    font-size: medium;
    padding: 0px 8px;
    line-height: 22px;
  }

  :global(.task-item) {
    background-color: rgb(206, 193, 188);
    width: 70%;
    margin: auto;
    border-radius: 10px;
  }

  :global(.task-item li) {
    padding: 10px;
  }

  :global(.task-item button) {
    width: 50px;
  }

  :global(.task-item button:hover){
    background-color: lightgoldenrodyellow;
  }

  :global(.task-item p) {
    font-size: 10px;
    color: rgb(12, 127, 121);
    padding-bottom: 10px;
  }

  :global(.task-item p::before) {
    content: "ℹ️ ";
  }


  @media only screen and (max-width: 600px) {
    input {
      width: 232px;
    }

    #tasksContainer {
      margin: auto;
    }

    :global(.task-item) {
      width: 95%;
    }
  }
</style>
