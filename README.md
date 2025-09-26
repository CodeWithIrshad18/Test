<asp:Content ID="Content1" ContentPlaceHolderID="HeadContent" runat="server">
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

        /* Step circle */
        .step-counter {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: #ccc;
            color: #fff;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 10px;
            z-index: 3;
            font-weight: bold;
            transition: 0.3s ease;
        }

        /* Step label (box style) */
        .step-name a {
            display: inline-block;
            padding: 8px 18px;
            border-radius: 6px;
            background: #e9ecef;
            color: #000;
            font-weight: 600;
            text-decoration: none;
            transition: 0.3s ease;
        }

        .step-name a:hover {
            background: #6c63ff;
            color: #fff;
        }

        /* Completed step */
        .stepper-item.completed .step-counter {
            background: #28a745;
        }
        .stepper-item.completed::before {
            border-top-color: #28a745;
        }
        .stepper-item.completed .step-name a {
            background: #28a745;
            color: #fff;
        }

        /* Active step */
        .stepper-item.active .step-counter {
            background: #007bff;
        }
        .stepper-item.active .step-name a {
            background: #007bff;
            color: #fff;
        }

        /* Upcoming step */
        .stepper-item.upcoming .step-counter {
            background: #ccc;
        }
        .stepper-item.upcoming .step-name a {
            background: #e9ecef;
            color: #000;
        }
    </style>
</asp:Content>


<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">
    <div class="container dashboard-container">
        <div class="text-center my-3 mt-4 mb-4">
            <h2 class="fw-bold text-uppercase text-dark display-6">Bonus</h2>
        </div>

        <div class="stepper-wrapper">
            <!-- Step 1 Completed -->
            <div class="stepper-item completed">
                <div class="step-counter">1</div>
                <div class="step-name">
                    <a href="Bonus_Generation.aspx" target="_blank">Bonus Generation</a>
                </div>
            </div>

            <!-- Step 2 Active -->
            <div class="stepper-item active">
                <div class="step-counter">2</div>
                <div class="step-name">
                    <a href="../Report/Bonus_Register_Report.aspx" target="_blank">Bonus Register Report</a>
                </div>
            </div>

            <!-- Step 3 Upcoming -->
            <div class="stepper-item upcoming">
                <div class="step-counter">3</div>
                <div class="step-name">
                    <a href="Bonus_Complaince_Entry.aspx" target="_blank">Bonus Compliance Entry</a>
                </div>
            </div>

            <!-- Step 4 Upcoming -->
            <div class="stepper-item upcoming">
                <div class="step-counter">4</div>
                <div class="step-name">
                    <a href="../Report/Bonus_Summary_Report.aspx" target="_blank">Bonus Compliance Report</a>
                </div>
            </div>
        </div>
    </div>
</asp:Content>




<asp:Content ID="Content1" ContentPlaceHolderID="HeadContent" runat="server">
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

        /* Step circle */
        .step-counter {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: #ccc;
            color: #fff;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 10px;
            z-index: 3;
            font-weight: bold;
            transition: 0.3s ease;
        }

        /* Step label (box) */
        .step-name a {
            display: inline-block;
            padding: 8px 18px;
            border-radius: 6px;
            background: #e9ecef;
            color: #000;
            font-weight: 600;
            text-decoration: none;
            transition: 0.3s ease;
        }

        .step-name a:hover {
            background: #6c63ff;
            color: #fff;
        }

        /* Completed step */
        .stepper-item.completed .step-counter {
            background: #28a745;
        }

        .stepper-item.completed::before {
            border-top-color: #28a745;
        }

        .stepper-item.completed .step-name a {
            background: #28a745;
            color: #fff;
        }

        /* Active step */
        .stepper-item.active .step-counter {
            background: #007bff;
        }

        .stepper-item.active .step-name a {
            background: #007bff;
            color: #fff;
        }

        /* Upcoming step */
        .stepper-item.upcoming .step-counter {
            background: #ccc;
        }

        .stepper-item.upcoming .step-name a {
            background: #e9ecef;
            color: #000;
        }
    </style>

    <script type="text/javascript">
        document.addEventListener("DOMContentLoaded", function () {
            // Check stored visited steps
            document.querySelectorAll(".step-link").forEach(link => {
                const stepId = link.dataset.step;

                if (localStorage.getItem(stepId) === "completed") {
                    link.closest(".stepper-item").classList.add("completed");
                    link.closest(".stepper-item").classList.remove("active", "upcoming");
                }

                // When user clicks → mark completed
                link.addEventListener("click", function () {
                    localStorage.setItem(stepId, "completed");
                    link.closest(".stepper-item").classList.add("completed");
                    link.closest(".stepper-item").classList.remove("active", "upcoming");
                });
            });
        });
    </script>
</asp:Content>


<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">
    <div class="container dashboard-container">
        <div class="text-center my-3 mt-4 mb-4">
            <h2 class="fw-bold text-uppercase text-dark display-6">Bonus</h2>
        </div>

        <div class="stepper-wrapper">
            <!-- Step 1 -->
            <div class="stepper-item active">
                <div class="step-counter">1</div>
                <div class="step-name">
                    <a class="card-link step-link" data-step="step1" href="Bonus_Generation.aspx" target="_blank">
                        Bonus Generation
                    </a>
                </div>
            </div>

            <!-- Step 2 -->
            <div class="stepper-item upcoming">
                <div class="step-counter">2</div>
                <div class="step-name">
                    <a class="card-link step-link" data-step="step2" href="../Report/Bonus_Register_Report.aspx" target="_blank">
                        Bonus Register Report
                    </a>
                </div>
            </div>

            <!-- Step 3 -->
            <div class="stepper-item upcoming">
                <div class="step-counter">3</div>
                <div class="step-name">
                    <a class="card-link step-link" data-step="step3" href="Bonus_Complaince_Entry.aspx" target="_blank">
                        Bonus Compliance Entry
                    </a>
                </div>
            </div>

            <!-- Step 4 -->
            <div class="stepper-item upcoming">
                <div class="step-counter">4</div>
                <div class="step-name">
                    <a class="card-link step-link" data-step="step4" href="../Report/Bonus_Summary_Report.aspx" target="_blank">
                        Bonus Compliance Report
                    </a>
                </div>
            </div>
        </div>
    </div>
</asp:Content>





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

