<script>
  import { Modal } from "svelte-simple-modal";

    export let text;
    export let onSave;
  
    let updatedText = text;
  
    function save() {
      let h = document.getElementById("Toast")
      if(updatedText===text){
        h.innerHTML="No changes were made"
      } else if (updatedText !== text){
        onSave(updatedText);
        h.innerHTML="✅ Update Successful!"
      }
    }

</script>

<div id="m">
  <h6 id="Toast"> </h6>
  <textarea
    bind:value={updatedText}
    style="width:200px;height:100px;resize:none;"
  />
  <button on:click={save}>Save</button>
</div>

<style>
  h6 {
    text-align: center;
  }

  #m {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
</style>
