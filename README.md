WHERE
(
    -- normal master tagging logic
    EXISTS
    (
        SELECT 1
        FROM Status_Tagging_Master STM
        WHERE STM.StatusName = A.Status
          AND STM.Tagged_PNO = @UserPNO
    )

    -- special case: Pending with Requester HOD
    OR
    (
        A.Status = 'Pending with Requester HOD'
        AND A.HOD_PNO = @UserPNO
    )
)


WHERE
(
    -- CASE 1 : Pending with Requester HOD
    (
        R.Status = 'Pending with HOD of Requester Dept'
        AND
        (
            R.HOD_Pno = @HodPno
            OR
            @LoginPno IN
            (
                SELECT Pno
                FROM App_Approval_Tagging_Master
                WHERE Level = 'Pending with HOD of Requester Dept'
            )
        )
    )

    OR

    -- CASE 2 : Other all statuses
    (
        R.Status <> 'Pending with HOD of Requester Dept'
        AND
        @LoginPno IN
        (
            SELECT Pno
            FROM App_Approval_Tagging_Master
            WHERE Level = R.Status
        )
    )
)
ORDER BY R.CreatedOn DESC



<div class="form-group col-md-3 mb-1">
    <label class="m-0 p-0 col-form-label-sm font-weight-bold fs-6">
        Location:<span class="text-danger">*</span>
    </label>

    <div id="locationTrigger"
         class="d-flex align-items-center gap-2 mt-1"
         style="cursor:pointer;">

        <!-- Google Maps style pin -->
        <i class="fas fa-map-marker-alt text-danger fs-5"></i>

        <span id="locationText"
              class="text-primary text-decoration-underline">
            View on Map
        </span>
    </div>
</div>

document.getElementById("locationTrigger").addEventListener("click", function () {

    var modal = new bootstrap.Modal(document.getElementById("mapModal"));
    modal.show();

    setTimeout(initMap, 300);
});



<asp:TextBox ID="Location"
             runat="server"
             ClientIDMode="Static"
             CssClass="d-none" />

<div class="form-group col-md-3 mb-1">
    <label class="m-0 p-0 col-form-label-sm font-weight-bold fs-6">
        Location:<span class="text-danger">*</span>
    </label>

    <div id="locationTrigger"
         class="form-control form-control-sm d-flex align-items-center justify-content-between cursor-pointer"
         style="cursor:pointer;">

        <span id="locationText" class="text-muted">
            View on Map
        </span>

        <i class="fas fa-map-marked-alt text-primary"></i>
    </div>
</div>

document.getElementById("locationTrigger").addEventListener("click", function () {

    var modal = new bootstrap.Modal(document.getElementById("mapModal"));
    modal.show();

    setTimeout(initMap, 300);
});

function updateLocationLabel() {
    let val = document.getElementById("Location").value;
    document.getElementById("locationText").innerText =
        val && val.trim() !== "" ? "Location Selected" : "View on Map";
}

updateLocationLabel();



if(isExist)
            {
                string oldRef = dsExist.Tables[0].Rows[0]["RefNo"].ToString();
                PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[0]["RefNo"] = oldRef;
                PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[0]["ResubmitedOn"] = System.DateTime.Now;
                

            }
            else
            { 
                DataSet ds = new DataSet();
                ds = blobj.GetDelete(vc, year, Period);

                if (PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[0].RowState == DataRowState.Modified)
                {

                    for (int i = 0; i < PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows.Count; i++)
                    {
                        PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[i]["VCode"] = Session["Username"].ToString();
                        PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[i]["Year"] = Year.SelectedValue;
                        PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[i]["Period"] = SearchPeriod.SelectedValue;
                        PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[i].AcceptChanges();
                        PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[i].SetAdded();
                        PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[i]["CreatedBy"] = Session["UserName"].ToString();
                        PageRecordDataSet.Tables["App_Half_Yearly_Details"].Rows[i]["CreatedOn"] = System.DateTime.Now;

                    }
                    

                }
            }










ye  code dkho thk se ,,, jb hum kiy the to resubmitted v rkhe hi theee to dkhoo ismmeee
