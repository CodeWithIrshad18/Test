<asp:Content ID="Content1" ContentPlaceHolderID="HeadContent" runat="server">
    
    <script type="text/javascript">


</script>

    <style>
  
        .stepper-wrapper {
  display: flex;
  justify-content: space-between;
  margin: 30px 0;
  position: relative;
}

.stepper-item {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 1;
}

.stepper-item::before {
  position: absolute;
  content: "";
  border-top: 2px solid #ccc;
  top: 20px;
  left: -50%;
  width: 100%;
  z-index: 2;
}

.stepper-item:first-child::before {
  content: none;
}

.step-counter {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: #6c63ff;
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 10px;
  z-index: 3;
  font-weight: bold;
}

.step-name {
  text-align: center;
  font-size: 14px;
}

.stepper-item.completed .step-counter {
  background: #28a745; 
}

.stepper-item.completed::before {
  border-top-color: #28a745;
}
    </style>


</asp:Content>

<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">
 <div class="container dashboard-container">
  <div class="text-center my-3 mt-4 mb-4">
    <h2 class="fw-bold text-uppercase text-dark display-6">Bonus</h2>
  </div>

  <div class="stepper-wrapper">
    <div class="stepper-item completed">
      <div class="step-counter">1</div>
      <div class="step-name">
        <a class="card-link" href="Bonus_Generation.aspx" target="_blank">
          Bonus Generation
        </a>
      </div>
    </div>

    <div class="stepper-item">
      <div class="step-counter">2</div>
      <div class="step-name">
        <a class="card-link" href="../Report/Bonus_Register_Report.aspx" target="_blank">
          Bonus Register Report
        </a>
      </div>
    </div>

    <div class="stepper-item">
      <div class="step-counter">3</div>
      <div class="step-name">
        <a class="card-link" href="Bonus_Complaince_Entry.aspx" target="_blank">
          Bonus Compliance Entry
        </a>
      </div>
    </div>

    <div class="stepper-item">
      <div class="step-counter">4</div>
      <div class="step-name">
        <a class="card-link" href="../Report/Bonus_Summary_Report.aspx" target="_blank">
          Bonus Compliance Report
        </a>
      </div>
    </div>
  </div>
</div>  
</asp:Content>


in this i want to show labels in the box type or circule

