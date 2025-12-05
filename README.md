 data-sourceid="f6851628-d446-4a7b-b569-31191c1d7d3a,085fa7a4-a5cc-424c-ae63-b524a2225f47" data-feederid="e43da0ea-9c88-4a1a-aafc-09bc234704cc,6f0e23ce-e251-46e2-a0fb-62bf13c1caa7"


refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();
        SourceMaster.style.display = "block";

        document.getElementById("DTRCapacity").value = this.getAttribute("data-DTRCapacity");
        document.getElementById("DTRName").value = this.getAttribute("data-DTRName");
        document.getElementById("NoOfConsumer").value = this.getAttribute("data-NoOfConsumer");
        document.getElementById("DTRId").value = this.getAttribute("data-id");

        let existingSourceIds = this.getAttribute("data-sourceid");
        if (existingSourceIds) {
            loadExistingSources(existingSourceIds);
        }

        if (deleteButton) {
            deleteButton.style.display = "inline-block";
        }
    });
});
