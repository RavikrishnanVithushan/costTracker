<template>
 <Header/>
 <div class="container">
  <Balance :total="+total"/>
  <IncomeExpense :income="+income" :expense="+expenses"/>
  <Transaction :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
  <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
 </div>
</template>

<script setup>

import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpense from './components/IncomeExpense.vue';
import Transaction from './components/Transaction.vue';
import AddTransaction from './components/AddTransaction.vue';
import { ref,computed,onMounted} from 'vue';
import {useToast} from 'vue-toastification';

const transactions = ref([
]);

onMounted (()=>{
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if(savedTransactions){
    transactions.value = savedTransactions;
  }
});

const toast = useToast();

const total = computed(()=>{
  return transactions.value.reduce((acc,transaction)=>{
    return acc+transaction.amount;
  },0);
});

//Get Income
const income = computed(()=>{
  return transactions.value
  .filter((transaction)=>transaction.amount > 0)
  .reduce((acc,transaction)=>{
    return acc+transaction.amount;
  },0).toFixed(2);
});

//Get Expense
const expenses = computed(()=>{
  return transactions.value
  .filter((transaction)=>transaction.amount < 0)
  .reduce((acc,transaction)=>{
    return acc+transaction.amount;
  },0).toFixed(2);
});

//Add transaction

const handleTransactionSubmitted = (transactionData)=>{
  transactions.value.push({
    id:generateUniqueId(),
    text:transactionData.text,
    amount:transactionData.amount
  })
  saveTransactionsToLocalStorage();
  toast.success("Your transaction Added!!");
}

//Generate Id

const generateUniqueId =()=>{
  return Math.floor(Math.random)*100000;
}

//Delete transaction
const handleTransactionDeleted = (id)=>{
transactions.value = transactions.value.filter(
  (transaction) => transaction.id !== id
);

saveTransactionsToLocalStorage();
toast.success('Transaction deleted successfully!!')
}

//saved to localStorage

const saveTransactionsToLocalStorage = () =>{
  localStorage.setItem('transactions',JSON.stringify(transactions.value));
}

</script>