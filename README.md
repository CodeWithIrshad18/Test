.stepper-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 30px auto;
    position: relative;
    width: 400px;   /* fixed equal width */
}

/* Single vertical line through the steps */
.stepper-wrapper::before {
    content: "";
    position: absolute;
    top: 0;
    bottom: 0;
    left: 50%;                  /* center of wrapper */
    transform: translateX(-50%);
    width: 4px;
    background: #ccc;           /* default line color */
    z-index: 0;
}

.stepper-item {
    position: relative;
    display: flex;
    align-items: center;
    width: 100%;
    margin-bottom: 40px;
    z-index: 1;   /* keep steps above line */
}

.step-counter {
    width: 45px;
    height: 45px;
    border-radius: 50%;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 20px;
    font-weight: bold;
    background: #ccc; /* fallback */
    flex-shrink: 0;
    z-index: 2;
    position: relative;
    left: -50%;               /* shift to align with line */
    transform: translateX(-50%);
}

.step-name {
    flex: 1;
    margin-left: 20px;         /* spacing from circle */
}

.step-name a {
    display: block;
    width: 100%;
    padding: 12px 20px;
    border-radius: 6px;
    background: #e9ecef;
    color: #000;
    font-weight: 600;
    text-decoration: none;
    transition: all 0.3s ease;
    box-shadow: 0 2px 6px rgba(0,0,0,0.1);
    text-align: left;
}

/* Hover effect */
.step-name a:hover {
    background: #6c63ff;
    color: #fff;
    transform: scale(1.05);
    box-shadow: 0 4px 12px rgba(0,0,0,0.2);
}

/* Status colors */
.stepper-item.completed .step-counter {
    background: linear-gradient(135deg, #28a745, #5cd879);
}
.stepper-item.active .step-counter {
    background: linear-gradient(135deg, #007bff, #66b2ff);
}
.stepper-item.upcoming .step-counter {
    background: linear-gradient(135deg, #ff8d00, #ffb347);
}
.stepper-item.upcoming2 .step-counter {
    background: linear-gradient(135deg, #9d00ff, #c266ff);
}




.stepper-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 30px auto;
    position: relative;
    width: 400px;   /* fixed equal width for all steps */
}

.stepper-item {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: flex-start;
    width: 100%;            /* make each step equal width */
    margin-bottom: 40px;
}

.stepper-item::before {
    position: absolute;
    content: "";
    border-left: 4px solid #ccc;
    top: -50%;
    left: 30px;   /* aligns with center of step-counter */
    height: 100%;
    z-index: 1;
}

.stepper-item:first-child::before {
    content: none;
}

.step-counter {
    width: 45px;
    height: 45px;
    border-radius: 50%;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 20px;
    z-index: 2;
    font-weight: bold;
    background: #ccc; /* fallback */
    flex-shrink: 0;
}

.step-name {
    flex: 1;   /* make text box take remaining space */
}

.step-name a {
    display: block;
    width: 100%;         /* all step names same width */
    text-align: center;  /* center text inside */
    padding: 12px 20px;
    border-radius: 6px;
    background: #e9ecef;
    color: #000;
    font-weight: 600;
    text-decoration: none;
    transition: all 0.3s ease;
    box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}

/* Hover */
.step-name a:hover {
    background: #6c63ff;
    color: #fff;
    transform: scale(1.05);
    box-shadow: 0 4px 12px rgba(0,0,0,0.2);
}

/* Status colors */
.stepper-item.completed .step-counter {
    background: linear-gradient(135deg, #28a745, #5cd879);
}
.stepper-item.completed::before {
    border-left: 4px solid #28a745;
}
.stepper-item.completed .step-name a {
    background: #28a74578;
}

.stepper-item.active .step-counter {
    background: linear-gradient(135deg, #007bff, #66b2ff);
}
.stepper-item.active .step-name a {
    background: #007bff7d;
}

.stepper-item.upcoming .step-counter {
    background: linear-gradient(135deg, #ff8d00, #ffb347);
}
.stepper-item.upcoming .step-name a {
    background: #ff8d0069;
}

.stepper-item.upcoming2 .step-counter {
    background: linear-gradient(135deg, #9d00ff, #c266ff);
}
.stepper-item.upcoming2 .step-name a {
    background: #9d00ff69;
}

.stepper-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 30px auto;
    position: relative;
    width: 400px;   /* fixed equal width for all steps */
}

/* Timeline vertical line */
.stepper-wrapper::before {
    content: "";
    position: absolute;
    top: 0;
    bottom: 0;
    left: 30px;            /* align with step circles */
    width: 4px;
    background: #ccc;      /* default line color */
    z-index: 0;
}

.stepper-item {
    position: relative;
    display: flex;
    align-items: center;
    width: 100%;
    margin-bottom: 40px;
    z-index: 1;   /* keeps step items above line */
}

.step-counter {
    width: 45px;
    height: 45px;
    border-radius: 50%;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 20px;
    font-weight: bold;
    background: #ccc; /* fallback */
    flex-shrink: 0;
    z-index: 2;
}

.step-name {
    flex: 1;
}

.step-name a {
    display: block;
    width: 100%;
    text-align: center;
    padding: 12px 20px;
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

/* Status colors */
.stepper-item.completed .step-counter {
    background: linear-gradient(135deg, #28a745, #5cd879);
}
.stepper-item.completed ~ .stepper-item::before {
    background: #28a745;
}
.stepper-item.completed .step-name a {
    background: #28a74578;
}

.stepper-item.active .step-counter {
    background: linear-gradient(135deg, #007bff, #66b2ff);
}
.stepper-item.active .step-name a {
    background: #007bff7d;
}

.stepper-item.upcoming .step-counter {
    background: linear-gradient(135deg, #ff8d00, #ffb347);
}
.stepper-item.upcoming .step-name a {
    background: #ff8d0069;
}

.stepper-item.upcoming2 .step-counter {
    background: linear-gradient(135deg, #9d00ff, #c266ff);
}
.stepper-item.upcoming2 .step-name a {
    background: #9d00ff69;
}





.stepper-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;   /* Center horizontally */
    margin: 30px auto;
    position: relative;
    width: fit-content;    /* Shrink to fit content */
}

.stepper-item {
    position: relative;
    display: flex;
    align-items: center;
    margin-bottom: 40px;
}

.stepper-item::before {
    position: absolute;
    content: "";
    border-left: 4px solid #ccc;
    top: -50%;
    left: 50%;                 /* Center line */
    transform: translateX(-50%);
    height: 100%;
    z-index: 2;
}

.stepper-item:first-child::before {
    content: none;
}

.step-counter {
    width: 45px;
    height: 45px;
    border-radius: 50%;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 20px;
    z-index: 3;
    font-weight: bold;
    opacity: 0;
    transform: scale(0.8);
    animation: popIn 0.6s forwards;
    animation-delay: 0.2s;
    background: #ccc; /* fallback */
}

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

/* Status colors */
.stepper-item.completed .step-counter {
    background: linear-gradient(135deg, #28a745, #5cd879);
}
.stepper-item.completed::before {
    border-left: 4px solid #28a745;
}
.stepper-item.completed .step-name a {
    background: #28a74578;
}

.stepper-item.active .step-counter {
    background: linear-gradient(135deg, #007bff, #66b2ff);
}
.stepper-item.active .step-name a {
    background: #007bff7d;
}

.stepper-item.upcoming .step-counter {
    background: linear-gradient(135deg, #ff8d00, #ffb347);
}
.stepper-item.upcoming .step-name a {
    background: #ff8d0069;
}

.stepper-item.upcoming2 .step-counter {
    background: linear-gradient(135deg, #9d00ff, #c266ff);
}
.stepper-item.upcoming2 .step-name a {
    background: #9d00ff69;
}

/* Animation */
@keyframes popIn {
    to {
        opacity: 1;
        transform: scale(1);
    }
}



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

      
        .stepper-item.active .step-counter {
            background: linear-gradient(135deg, #007bff, #66b2ff);
        }
     
        .stepper-item.active .step-name a {
            background: #007bff7d;
            color: #000;
        }

      
        .stepper-item.upcoming .step-counter {
            background: linear-gradient(135deg, #ff8d00, #ffb347);
        }
        .stepper-item.upcoming .step-name a {
            background: #ff8d0069;
            color: #000;
        }

       
        .stepper-item.upcoming2 .step-counter {
            background: linear-gradient(135deg, #9d00ff, #c266ff);
        }
        .stepper-item.upcoming2 .step-name a {
            background: #9d00ff69;
            color: #000;
        }

     
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
