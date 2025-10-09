if (deleteButton) {
    deleteButton.addEventListener("click", function () {
        if (confirm("Are you sure you want to delete this Subject?")) {
            actionTypeInput.value = "delete";  
            document.getElementById("form").submit();  
        }
    });
}
