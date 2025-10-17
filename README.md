<div class="input-group input-group-sm">
  <span class="input-group-text">${period}</span>
  <input type="text" class="form-control" name="Target_${period}" placeholder="Target">
</div>

periods.forEach(period => {
    const div = document.createElement("div");
    div.classList.add("col-md-6", "col-lg-4", "mb-2");
    div.innerHTML = `
        <div class="border rounded p-2 bg-light">
            <label class="form-label mb-1 fw-semibold" style="font-size: 0.85rem;">${period}</label>
            <input type="text" class="form-control form-control-sm" name="Target_${period}" placeholder="Enter target value">
        </div>
    `;
    periodicityContainer.appendChild(div);
});

#periodicityContainer .border {
    background-color: #f8f9fa;
    transition: box-shadow 0.2s ease-in-out;
}
#periodicityContainer .border:hover {
    box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}
#periodicityContainer label {
    color: #495057;
}

periods.forEach(period => {
    let colClass = "col-md-3"; // default (Monthly)
    const total = periods.length;

    if (total <= 4) {
        colClass = "col-md-6 col-lg-6"; // Quarterly
    } else if (total === 1) {
        colClass = "col-12"; // Yearly
    } else if (total <= 6) {
        colClass = "col-md-4"; // Half-yearly etc.
    }

    const div = document.createElement("div");
    div.className = `${colClass} mb-2`;

    div.innerHTML = `
        <div class="border rounded p-2 bg-light">
            <label class="form-label mb-1 fw-semibold text-truncate" title="${period}" style="font-size: 0.85rem; display:block;">
                ${period}
            </label>
            <input type="text" class="form-control form-control-sm" 
                   name="Target_${period.replace(/[^a-zA-Z0-9]/g, '_')}" 
                   placeholder="Enter target value">
        </div>
    `;
    periodicityContainer.appendChild(div);
});

#periodicityContainer .border {
    background-color: #f8f9fa;
    transition: box-shadow 0.2s ease-in-out;
}
#periodicityContainer .border:hover {
    box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}
#periodicityContainer label {
    color: #495057;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}




i have this labels for quartly and labels are not showing properly ,design is bad when Quartly is selected 
PeriodicityName
Apr - Jun (Q1 - 1st Qtr)
Jul - Sep (Q2 - 2nd Qtr)
Oct - Dec (Q3 - 3rd Qtr)
Jan - Mar (Q4 - 4th Qtr)
