0
: 
{periodicityTransactionID: '2016', targetValue: '70%'}
1
: 
{periodicityTransactionID: '2025', targetValue: '70%'}
2
: 
{periodicityTransactionID: '2017', targetValue: '70%'}
3
: 
{periodicityTransactionID: '2012', targetValue: '70%'}
4
: 
{periodicityTransactionID: '2018', targetValue: '70%'}
5
: 
{periodicityTransactionID: '2021', targetValue: '70%'}
6
: 
{periodicityTransactionID: '2024', targetValue: '70%'}
7
: 
{periodicityTransactionID: '2020', targetValue: '70%'}
8
: 
{periodicityTransactionID: '2009', targetValue: '70%'}
9
: 
{periodicityTransactionID: '2023', targetValue: '70%'}
10
: 
{periodicityTransactionID: '2022', targetValue: '70%'}
11
: 
{periodicityTransactionID: '2010', targetValue: '70%'}
12
: 
{periodicityTransactionID: '2008', targetValue: '70%'}
13
: 
{periodicityTransactionID: '2011', targetValue: '70%'}
14
: 
{periodicityTransactionID: '2026', targetValue: '70%'}
15
: 
{periodicityTransactionID: '2013', targetValue: '70%'}
16
: 
{periodicityTransactionID: '2019', targetValue: '70%'}



periods.forEach((period, index) => {
    let colClass = "col-md-3"; 
    if (total <= 4) colClass = "col-md-6"; 
    else if (total === 1) colClass = "col-12"; 
    else if (total <= 6) colClass = "col-md-4";

    const existingValue = targetMap[period] || "";

    const div = document.createElement("div");
    div.className = `${colClass} mb-2`;

    div.innerHTML = `
        <div class="input-group input-group-sm flex-nowrap">
            <span class="input-group-text text-truncate" 
                  style="max-width: 200px;" 
                  title="${period}">
                ${period}
            </span>

            <input type="hidden" 
                   name="TargetDetails[${index}].PeriodicityTransactionID" 
                   value="${period}" />

            <input type="text" class="form-control" 
                   name="TargetDetails[${index}].TargetValue" 
                   placeholder="Target" 
                   autocomplete="off"
                   value="${existingValue}">
        </div>
    `;
    periodicityContainer.appendChild(div);
});


issue is that data is coming this is in console i am checking 
