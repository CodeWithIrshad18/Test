    const div = document.createElement("div");
    div.className = `${colClass} mb-2`;

    div.innerHTML = `
        <div class="input-group input-group-sm flex-nowrap">
            <span class="input-group-text text-truncate" 
                  style="max-width: 200px;" 
                  title="${period}">
                ${period}
            </span>
            <input type="text" class="form-control" 
                   name="Target_${period.replace(/[^a-zA-Z0-9]/g, '_')}" 
                   placeholder="Target">
        </div>
    `;
    periodicityContainer.appendChild(div);
});
