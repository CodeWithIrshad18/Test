function renderFeederList(data) {
    let feederList = document.getElementById("FeederList");
    feederList.innerHTML = "";

    if (data.length === 0) {
        feederList.innerHTML = "<li class='ms-2 text-danger'>No Feeder Found</li>";
        return;
    }

    data.forEach(item => {

        let id = item.ID || item.id;
        let name = item.FeederName || item.feederName;

        console.log("Parsed:", id, name);

        feederList.innerHTML += `
            <li style="margin-left:5%;">
                <div class="form-check">
                    <input type="checkbox" class="form-check-input Feeder-checkbox"
                           value="${id}" id="f_${id}" />
                    <label class="form-check-label" for="f_${id}">${name}</label>
                </div>
            </li>`;
    });

    attachFeederEvents();
}




console output : 

2DTRMaster:436 Parsed: e43da0ea-9c88-4a1a-aafc-09bc234704cc testing for feedeer
DTRMaster:436 Parsed: 6f0e23ce-e251-46e2-a0fb-62bf13c1caa7 test feeder
