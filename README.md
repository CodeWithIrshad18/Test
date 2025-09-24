<img src='<%# ResolveUrl("~/upload/" + Eval("Attachments")) %>' 
     alt="Image" style="width:100%; height:300px;" />

<img src='/upload/<%# Eval("Attachments") %>' 
     alt="Image" style="width:100%; height:300px;" />

if (ds1.Tables[0].Rows.Count > 0)
{
    rptImages.DataSource = ds1.Tables[0];
    rptImages.DataBind();
}



i have this 2 image path 

<asp:Repeater ID="rptImages" runat="server">
                    <ItemTemplate>
                        <div class="swiper-slide">
                           <img src='<%# Eval("Attachments") %>' alt="Image" style="width:100%; height:300px;" />
                        </div>
                    </ItemTemplate>
                </asp:Repeater>


                    <div class="swiper mySwiper">
                    <div class="swiper-wrapper">
                        <asp:Repeater ID="Repeater1" runat="server">
                            <ItemTemplate>
                                <div class="swiper-slide">
                                   <img src='<%# ResolveUrl(Server.MapPath("~/upload/") + Eval("Attachments")) %>' alt="Image" style="width:100%; height:300px;" />
                                </div>
                            </ItemTemplate>
                        </asp:Repeater>


protected void Page_Load(object sender, EventArgs e)
        {
            if (!IsPostBack)
            {
                BL_Module_Master blobj1 = new BL_Module_Master();
                DataSet ds1 = new DataSet();
                ds1 = blobj1.getAttach();

                if (ds1.Tables[0].Rows.Count > 0)
                {
                    string imageUrl = ds1.Tables[0].Rows[0]["Attachments"].ToString();
                    //imgSlider.ImageUrl = imageUrl;
                    rptImages.DataSource = ds1.Tables[0];
                  
                    rptImages.DataBind();
                }

            }

             
            //BindSlider();


        }

this is the path i am getting in network tab in developer tool , i have attachment in upload folder 
