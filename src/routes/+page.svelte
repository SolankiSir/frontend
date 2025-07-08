<script>
    import { onMount } from 'svelte';

    let transactions = [];
    let form = {
        type: 'expense',
        amount: '',
        category: '',
        date: '',
        note: ''
    };

    // ✅ Use your live backend URL here
    const API_URL = 'https://backend-o5ll.onrender.com';

    async function loadTransactions() {
        const res = await fetch(`${API_URL}/api/transactions`);
        transactions = await res.json();
    }

    async function addTransaction() {
        await fetch(`${API_URL}/api/transaction`, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(form)
        });
        form = { type: 'expense', amount: '', category: '', date: '', note: '' };
        loadTransactions();
    }

    async function deleteTransaction(id) {
        await fetch(`${API_URL}/api/transaction/${id}`, { method: 'DELETE' });
        loadTransactions();
    }

    onMount(loadTransactions);
</script>

<h1>SmartBudget – Personal Finance Tracker</h1>

<form on:submit|preventDefault={addTransaction}>
    <select bind:value={form.type}>
        <option value="income">Income</option>
        <option value="expense">Expense</option>
    </select>
    <input type="number" bind:value={form.amount} placeholder="Amount" required />
    <input type="text" bind:value={form.category} placeholder="Category" required />
    <input type="date" bind:value={form.date} required />
    <input type="text" bind:value={form.note} placeholder="Note" />
    <button type="submit">Add</button>
</form>

<hr />

<h2>Transactions</h2>
{#if transactions.length === 0}
    <p>No transactions found.</p>
{:else}
    <ul>
        {#each transactions as tx}
            <li>
                [{tx.date}] {tx.type.toUpperCase()} – ₹{tx.amount} – {tx.category} – {tx.note}
                <button on:click={() => deleteTransaction(tx.id)}>Delete</button>
            </li>
        {/each}
    </ul>
{/if}
