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
            border-top: 4px solid #ccc;
            top: 20px;
            left: -50%;
            width: 100%;
            z-index: 2;
        }

        .stepper-item:first-child::before {
            content: none;
        }

        /* Circle counter */
        .step-counter {
            width: 45px;
            height: 45px;
            border-radius: 50%;
            color: #fff;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 10px;
            z-index: 3;
            font-weight: bold;
            opacity: 0;
            transform: scale(0.8);
            animation: popIn 0.6s forwards;
            animation-delay: 0.2s;
        }

        @keyframes popIn {
            to {
                opacity: 1;
                transform: scale(1);
            }
        }

        /* Step label box */
        .step-name a {
            display: inline-block;
            padding: 10px 20px;
            border-radius: 6px;
            background: #e9ecef;
            color: #000;
            font-weight: 600;
            text-decoration: none;
            transition: all 0.3s ease;
            box-shadow: 0 2px 6px rgba(0,0,0,0.1);
        }

        .step-name a:hover {
            background: #6c63ff;
            color: #fff;
            transform: scale(1.05);
            box-shadow: 0 4px 12px rgba(0,0,0,0.2);
        }

        /* Completed Step */
        .stepper-item.completed .step-counter {
            background: linear-gradient(135deg, #28a745, #5cd879);
        }
        .stepper-item.completed::before {
            border-top: 4px solid #28a745;
        }
        .stepper-item.completed .step-name a {
            background: #28a74578;
            color: #000;
        }

        /* Active Step */
        .stepper-item.active .step-counter {
            background: linear-gradient(135deg, #007bff, #66b2ff);
        }
        .stepper-item.active::before {
            border-top: 4px dashed #007bff;
        }
        .stepper-item.active .step-name a {
            background: #007bff7d;
            color: #000;
        }

        /* Upcoming Step */
        .stepper-item.upcoming .step-counter {
            background: linear-gradient(135deg, #ff8d00, #ffb347);
        }
        .stepper-item.upcoming .step-name a {
            background: #ff8d0069;
            color: #000;
        }

        /* Another Upcoming Style */
        .stepper-item.upcoming2 .step-counter {
            background: linear-gradient(135deg, #9d00ff, #c266ff);
        }
        .stepper-item.upcoming2 .step-name a {
            background: #9d00ff69;
            color: #000;
        }

        /* Responsive (Mobile view) */
        @media (max-width: 768px) {
            .stepper-wrapper {
                flex-direction: column;
                align-items: flex-start;
            }
            .stepper-item {
                flex-direction: row;
                margin-bottom: 25px;
            }
            .step-counter {
                margin-right: 15px;
                margin-bottom: 0;
            }
            .step-name a {
                padding: 8px 15px;
            }
        }
    </style>
</asp:Content>

<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">
    <div class="dashboard-container">
        <div class="text-center my-3 mt-4 mb-4">
            <h2 class="fw-bold text-uppercase text-dark display-6" style="font-weight:700;">Bonus</h2>
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
            <div class="stepper-item upcoming2">
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

     
        .stepper-item.completed .step-counter {
            background: #28a745;
        }
        .stepper-item.completed::before {
            border-top-color: #28a745;
        }
        .stepper-item.completed .step-name a {
            background: #28a74578;
            color: #000000;
        }

       
        .stepper-item.active .step-counter {
            background: #007bff;
        }
        .stepper-item.active .step-name a {
            background: #007bff7d;
            color: #000000;
        }

      
        .stepper-item.upcoming .step-counter {
            background:#ff8d00;
        }
        .stepper-item.upcoming .step-name a {
            background: #ff8d0069;
            color: #000000;
        }

        .stepper-item.upcoming2 .step-counter {
    background:#9d00ff;
}
.stepper-item.upcoming2 .step-name a {
    background: #9d00ff69;
    color: #000000;
}
    </style>
</asp:Content>
<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">
    <div class="dashboard-container">
        <div class="text-center my-3 mt-4 mb-4">
            <h2 class="fw-bold text-uppercase text-dark display-6" style="font-weight:700;">Bonus</h2>
        </div>

        <div class="stepper-wrapper">
         
            <div class="stepper-item completed">
                <div class="step-counter">1</div>
                <div class="step-name">
                    <a href="Bonus_Generation.aspx" target="_blank">Bonus Generation</a>
                </div>
            </div>

          
            <div class="stepper-item active">
                <div class="step-counter">2</div>
                <div class="step-name">
                    <a href="../Report/Bonus_Register_Report.aspx" target="_blank">Bonus Register Report</a>
                </div>
            </div>

           
            <div class="stepper-item upcoming">
                <div class="step-counter">3</div>
                <div class="step-name">
                    <a href="Bonus_Complaince_Entry.aspx" target="_blank">Bonus Compliance Entry</a>
                </div>
            </div>

         
            <div class="stepper-item upcoming2">
                <div class="step-counter">4</div>
                <div class="step-name">
                    <a href="../Report/Bonus_Summary_Report.aspx" target="_blank">Bonus Compliance Report</a>
                </div>
            </div>
        </div>
    </div>
</asp:Content>


