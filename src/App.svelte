<script>
  import { afterUpdate, onMount, setContext } from "svelte";
  // components
  import ExpenseForm from "./ExpenseForm.svelte";
  import ExpensesList from "./ExpensesList.svelte";
  import Navbar from "./Navbar.svelte";
  import Total from "./Total.svelte";
  import Modal from "./Modal.svelte";

  // variables
  let expenses = [];

  let setId = null;
  let setName = "";
  let setAmount = null;

  let isFormOpen = false;

  $: isEditing = setId ? true : false;
  $: total = expenses.reduce((acc, curr) => {
    return (acc += curr.amount);
  }, 0);

  // lifecycle
  onMount(() => {
    expenses = localStorage.getItem("expenses")
      ? JSON.parse(localStorage.getItem("expenses"))
      : [];
  });

  afterUpdate(() => {
    updateLocalStorage();
  });

  // functions
  function addExpense({ name, amount }) {
    console.log(setId);
    let expense = { id: Math.random() * Date.now(), name, amount };
    expenses = [expense, ...expenses];
  }

  function clearExpenses() {
    expenses = [];
  }

  function clearSetVariables() {
    setAmount = null;
    setId = null;
    setName = "";
  }

  function hideForm() {
    isFormOpen = false;
    clearSetVariables();
  }

  function editExpense({ amount, name }) {
    expenses = expenses.map((expense) => {
      return expense.id === setId
        ? { ...expense, name, amount }
        : { ...expense };
    });
    clearSetVariables();
  }

  function removeExpense(id) {
    expenses = expenses.filter((expense) => expense.id != id);
  }

  function setModifyExpense(id) {
    let expense = expenses.find((expense) => expense.id === id);

    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;
    showForm();
  }

  function showForm() {
    isFormOpen = true;
  }

  function updateLocalStorage() {
    localStorage.setItem("expenses", JSON.stringify(expenses));
  }

  setContext("hideForm", hideForm);
  setContext("removeExpense", removeExpense);
  setContext("setExpense", setModifyExpense);
  setContext("showForm", showForm);
</script>

<!-- <style></style> -->
<!-- CSS/STYLING -->

<Navbar {showForm} />
<main class="content">
  {#if isFormOpen}
    <Modal>
      <ExpenseForm
        {addExpense}
        {editExpense}
        {hideForm}
        name={setName}
        amount={setAmount}
        {isEditing}
      />
    </Modal>
  {/if}
  <Total title="Total Expenses" {total} />
  <ExpensesList {expenses} />
  <button
    type="button"
    class="btn btn-primary btn-block"
    on:click={clearExpenses}
    >clear expenses
  </button>
</main>
