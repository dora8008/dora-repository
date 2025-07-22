document.addEventListener('DOMContentLoaded', () => {
    const expenseForm = document.getElementById('expenseForm');
    const expenseDescription = document.getElementById('expenseDescription');
    const expenseAmount = document.getElementById('expenseAmount');
    const expenseDate = document.getElementById('expenseDate');
    const expenseList = document.getElementById('expenseList');
    const currentMonthTotalDisplay = document.getElementById('currentMonthTotal');
    const currentMonthYearDisplay = document.getElementById('currentMonthYear');
    const historyList = document.getElementById('historyList');
    const resetMonthBtn = document.getElementById('resetMonthBtn');
    const clearHistoryBtn = document.getElementById('clearHistoryBtn');

    expenseDate.value = new Date().toISOString().split('T')[0];

    let allExpenses = [];
    let history = [];
    let currentActiveMonth = '';

    const generateId = () => {
        return Math.random().toString(36).substr(2, 9);
    };

    const formatCurrency = (amount) => {
        return new Intl.NumberFormat('en-IN', {
            style: 'currency',
            currency: 'INR',
            minimumFractionDigits: 2
        }).format(amount);
    };

    const getMonthYear = (dateString = null) => {
        const date = dateString ? new Date(dateString) : new Date();
        return `${date.getFullYear()}-${(date.getMonth() + 1).toString().padStart(2, '0')}`;
    };

    const loadData = () => {
        const storedAllExpenses = localStorage.getItem('allExpenses');
        const storedCurrentActiveMonth = localStorage.getItem('currentActiveMonth');

        if (storedAllExpenses) {
            allExpenses = JSON.parse(storedAllExpenses);
        }

        const todayMonth = getMonthYear();
        if (storedCurrentActiveMonth) {
            currentActiveMonth = storedCurrentActiveMonth;
        } else {
            currentActiveMonth = todayMonth;
        }

        if (currentActiveMonth !== todayMonth) {
            processPreviousMonthDataAndReset(currentActiveMonth);
            currentActiveMonth = todayMonth;
        }

        rebuildHistoryFromExpenses();
        saveData();
    };

    const saveData = () => {
        localStorage.setItem('allExpenses', JSON.stringify(allExpenses));
        localStorage.setItem('history', JSON.stringify(history));
        localStorage.setItem('currentActiveMonth', currentActiveMonth);
    };

    const addExpense = (description, amount, date) => {
        const newExpense = {
            id: generateId(),
            description,
            amount: parseFloat(amount),
            date: date
        };
        allExpenses.push(newExpense);
        saveData();
        rebuildHistoryFromExpenses();
        renderExpenses();
        updateTotalDisplay();

        const expenseMonth = getMonthYear(date);
        if (expenseMonth !== currentActiveMonth) {
            alert(`Expense for ${date} (a past month) has been added and updated in history.`);
        }
    };

    const deleteExpense = (id) => {
        allExpenses = allExpenses.filter(expense => expense.id !== id);
        saveData();
        rebuildHistoryFromExpenses();
        renderExpenses();
        updateTotalDisplay();
    };

    const getExpensesForCurrentMonth = () => {
        return allExpenses.filter(expense => getMonthYear(expense.date) === currentActiveMonth);
    };

    const calculateTotal = () => {
        const currentMonthExpenses = getExpensesForCurrentMonth();
        return currentMonthExpenses.reduce((sum, expense) => sum + expense.amount, 0);
    };

    const renderExpenses = () => {
        expenseList.innerHTML = '';
        const currentMonthExpenses = getExpensesForCurrentMonth();

        if (currentMonthExpenses.length === 0) {
            expenseList.innerHTML = `
                <tr>
                    <td colspan="4" class="text-center text-muted">No expenses yet for this month.</td>
                </tr>
            `;
            resetMonthBtn.classList.add('d-none');
            return;
        }

        currentMonthExpenses.sort((a, b) => new Date(b.date) - new Date(a.date));

        currentMonthExpenses.forEach(expense => {
            const row = document.createElement('tr');
            row.innerHTML = `
                <td>${expense.date}</td>
                <td>${expense.description}</td>
                <td><span class="badge bg-success">${formatCurrency(expense.amount)}</span></td>
                <td>
                    <button class="btn btn-danger btn-sm delete-btn" data-id="${expense.id}">
                        <i class="fas fa-trash-alt"></i> Delete
                    </button>
                </td>
            `;
            expenseList.appendChild(row);
        });

        resetMonthBtn.classList.remove('d-none');
    };

    const updateTotalDisplay = () => {
        const total = calculateTotal();
        currentMonthTotalDisplay.textContent = formatCurrency(total);
        const [year, monthNum] = currentActiveMonth.split('-');
        const monthName = new Date(year, monthNum - 1, 1).toLocaleString('en-IN', { month: 'long' });
        currentMonthYearDisplay.textContent = `${monthName} ${year}`;
    };

    const renderHistory = () => {
        historyList.innerHTML = '';

        if (history.length === 0) {
            historyList.innerHTML = `
                <tr>
                    <td colspan="2" class="text-center text-muted">No history yet.</td>
                </tr>
            `;
            clearHistoryBtn.classList.add('d-none');
            return;
        }

        history.sort((a, b) => b.month.localeCompare(a.month));

        history.forEach(item => {
            const row = document.createElement('tr');
            const [year, monthNum] = item.month.split('-');
            const monthName = new Date(year, monthNum - 1, 1).toLocaleString('en-IN', { month: 'long' });

            row.innerHTML = `
                <td>${monthName} ${year}</td>
                <td>${formatCurrency(item.total)}</td>
            `;
            historyList.appendChild(row);
        });

        clearHistoryBtn.classList.remove('d-none');
    };

    const rebuildHistoryFromExpenses = () => {
        const tempHistoryMap = {};

        allExpenses.forEach(expense => {
            const monthYear = getMonthYear(expense.date);
            if (monthYear !== currentActiveMonth) {
                if (!tempHistoryMap[monthYear]) {
                    tempHistoryMap[monthYear] = 0;
                }
                tempHistoryMap[monthYear] += expense.amount;
            }
        });

        history = [];
        for (const month in tempHistoryMap) {
            if (tempHistoryMap.hasOwnProperty(month)) {
                history.push({
                    month: month,
                    total: tempHistoryMap[month]
                });
            }
        }
        saveData();
        renderHistory();
    };

    const processPreviousMonthDataAndReset = (monthToArchive) => {
        const expensesToArchive = allExpenses.filter(expense => getMonthYear(expense.date) === monthToArchive);
        const totalSpentArchivedMonth = expensesToArchive.reduce((sum, expense) => sum + expense.amount, 0);
        allExpenses = allExpenses.filter(expense => getMonthYear(expense.date) !== monthToArchive);
        saveData();

        if (totalSpentArchivedMonth > 0) {
            const [year, monthNum] = monthToArchive.split('-');
            const monthName = new Date(year, monthNum - 1, 1).toLocaleString('en-IN', { month: 'long' });
            alert(`Month ended! Your total of ${formatCurrency(totalSpentArchivedMonth)} for ${monthName} ${year} has been archived.`);
        }
    };

    const clearAllHistory = () => {
        if (confirm('Are you sure you want to clear ALL monthly spending history? This action cannot be undone.')) {
            allExpenses = allExpenses.filter(expense => getMonthYear(expense.date) === currentActiveMonth);
            history = [];
            saveData();
            rebuildHistoryFromExpenses();
            renderHistory();
            alert('Monthly spending history has been cleared.');
        }
    };

    expenseForm.addEventListener('submit', (e) => {
        e.preventDefault();
        const description = expenseDescription.value.trim();
        const amount = expenseAmount.value.trim();
        const date = expenseDate.value;

        if (description && amount && date) {
            addExpense(description, amount, date);
            expenseDescription.value = '';
            expenseAmount.value = '';
            expenseDate.value = new Date().toISOString().split('T')[0];
            expenseDescription.focus();
        } else {
            alert('Please enter description, amount, and select a date.');
        }
    });

    expenseList.addEventListener('click', (e) => {
        if (e.target.classList.contains('delete-btn') || e.target.closest('.delete-btn')) {
            const button = e.target.closest('.delete-btn');
            const idToDelete = button.dataset.id;
            if (confirm('Are you sure you want to delete this expense?')) {
                deleteExpense(idToDelete);
            }
        }
    });

    resetMonthBtn.addEventListener('click', () => {
        if (confirm('Are you sure you want to manually reset the month? This will archive current month\'s data and clear current expenses.')) {
            processPreviousMonthDataAndReset(currentActiveMonth);
            currentActiveMonth = getMonthYear();
            saveData();
            rebuildHistoryFromExpenses();
            renderExpenses();
            updateTotalDisplay();
        }
    });

    clearHistoryBtn.addEventListener('click', clearAllHistory);

    loadData();
    renderExpenses();
    updateTotalDisplay();
    renderHistory();
});
