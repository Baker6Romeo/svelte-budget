<script>
  import Title from "./Title.svelte";

  export let addExpense;
  export let amount = null;
  export let editExpense;
  export let hideForm;
  export let isEditing;
  export let name = "";

  $: isEmpty = !amount || !name;

  function handleSubmit() {
    if (isEditing) {
      console.log(amount, name);
      editExpense({ amount, name });
    } else {
      addExpense({ name, amount });
    }
    clearForm();
    hideForm();
  }

  function clearForm() {
    name = "";
    amount = null;
  }
</script>

<section class="form">
  <Title title="add expense" />
  <button class="close-btn" type="button" on:click={hideForm}
    ><i class="fas fa-times" />close</button
  >
  <form class="expense-form" on:submit|preventDefault={handleSubmit}>
    <div class="form-control">
      <label for="name">name</label>
      <input type="text" id="name" bind:value={name} />
    </div>
    <div class="form-control">
      <label for="amount">amount</label>
      <input type="number" id="amount" bind:value={amount} />
    </div>
    {#if isEmpty}
      <p class="form-empty">please fill out all form fields</p>
    {/if}
    <button
      class="btn btn-block"
      type="submit"
      disabled={isEmpty}
      class:disabled={isEmpty}
      >{#if isEditing}
        save changes
      {:else}
        add expense
      {/if}</button
    >
  </form>
</section>
