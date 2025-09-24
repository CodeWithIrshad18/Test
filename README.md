<asp:Content ID="Content1" ContentPlaceHolderID="HeadContent" runat="server">

    <!-- Swiper JS + CSS -->
    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/swiper/swiper-bundle.min.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/swiper/swiper-bundle.min.css" />

    <script type="text/javascript">
        document.addEventListener("DOMContentLoaded", function () {
            var swiper = new Swiper(".mySwiper", {
                loop: true,
                autoplay: {
                    delay: 3000,
                    disableOnInteraction: false
                },
                pagination: {
                    el: ".swiper-pagination",
                    clickable: true
                },
                navigation: {
                    nextEl: ".swiper-button-next",
                    prevEl: ".swiper-button-prev"
                }
            });
        });
    </script>

</asp:Content>

<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

    <div class="card m-2 shadow-lg">
        <div>
            <uc1:MyMsgBox ID="MyMsgBox" runat="server" />
        </div>       

        <div class="card-body pt-1">
            <div class="card-header bg-info text-light">
                <h6 class="m-0">Module Attachments</h6>
            </div>

            <!-- Swiper container -->
            <div class="swiper mySwiper">
                <div class="swiper-wrapper">
                    <asp:Repeater ID="rptImages" runat="server">
                        <ItemTemplate>
                            <div class="swiper-slide">
                                <img src='<%# ResolveUrl("~/upload/" + Eval("Attachments")) %>' 
                                     alt="Image" style="width:100%; height:300px; object-fit:cover;" />
                            </div>
                        </ItemTemplate>
                    </asp:Repeater>
                </div>

                <!-- Navigation buttons -->
                <div class="swiper-button-next"></div>
                <div class="swiper-button-prev"></div>

                <!-- Pagination dots -->
                <div class="swiper-pagination"></div>
            </div>
        </div>
    </div>

</asp:Content>


protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        BL_Module_Master blobj1 = new BL_Module_Master();
        DataSet ds1 = blobj1.getAttach();

        if (ds1 != null && ds1.Tables.Count > 0 && ds1.Tables[0].Rows.Count > 0)
        {
            // Bind your repeater to the dataset
            rptImages.DataSource = ds1.Tables[0];
            rptImages.DataBind();
        }
    }
}



<asp:Content ID="Content1" ContentPlaceHolderID="HeadContent" runat="server">

    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/swiper/swiper-bundle.min.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/swiper/swiper-bundle.min.css" />

<script type="text/javascript">

    var swiper = new Swiper(".mySwiper", {
        loop: true,
        autoplay: { delay: 3000 },
        pagination: { el: ".swiper-pagination", clickable: true },
        navigation: { nextEl: ".swiper-button-next", prevEl: ".swiper-button-prev" }
    });
</script>

</asp:Content>
<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

    <div class="card m-2 shadow-lg">

               <div>
                  <uc1:MyMsgBox ID="MyMsgBox" runat="server"/>
                        </div>       
            <asp:Literal ID="Literal1" runat="server"></asp:Literal>      
            <div class="card-body pt-1">
                         <div class="card-header bg-info text-light">
                    <h6 class="m-0">Module Attachments</h6>
                </div>

               
                                                           
                    <asp:Repeater ID="rptImages" runat="server">
                    <ItemTemplate>
                        <div class="swiper-slide">
                           <img src='<%# ResolveUrl("~/upload/" + Eval("Attachments")) %>' 
     alt="Image" style="width:100%; height:300px;" />
                        </div>
                    </ItemTemplate>
                </asp:Repeater>


                    <div class="swiper mySwiper">
                    <div class="swiper-wrapper">
                        <asp:Repeater ID="Repeater1" runat="server">
                            <ItemTemplate>
                                <div class="swiper-slide">
                                  <img src='<%# ResolveUrl("~/upload/" + Eval("Attachments")) %>' 
     alt="Image" style="width:100%; height:300px;" />
                                </div>
                            </ItemTemplate>
                        </asp:Repeater>
                    </div>
                    <!-- Add navigation buttons -->
                    <div class="swiper-button-next"></div>
                    <div class="swiper-button-prev"></div>
                    <!-- Add pagination -->
                    <div class="swiper-pagination"></div>
                 </div>

       
</div>
  </div>
</asp:Content>

i want to use slider , one image per slide then automatically it slides and also there is navigation button to slide , please check the code and provide me the error free to use
