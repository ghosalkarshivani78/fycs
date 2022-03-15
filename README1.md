using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Data.SqlClient;
using System.Configuration;
using System.Data;
using MySql.Data.MySqlClient;
namespace firstask
{
    public partial class edit : System.Web.UI.Page
    {
        static string strcon = ConfigurationManager.ConnectionStrings["taskformEntities"].ConnectionString;
        MySqlConnection con = new MySqlConnection(strcon);
        protected void Page_Load(object sender, EventArgs e)
        {
           
            string id = Request.QueryString["id"];
            string sql = "select * from task  where id='" + id + "'  ";
            con.Open();
            MySqlDataAdapter sda = new MySqlDataAdapter(sql, con);
            DataTable dt = new DataTable();
            sda.Fill(dt);
            repeater2.DataSource = dt;
            repeater2.DataBind();
            con.Close();
        }
       
        protected void update(object sender, EventArgs e)
        {
            try
            {
                string id = Request.QueryString["id"];
                RepeaterItem item = (sender as Button).NamingContainer as RepeaterItem;
                string fname = (item.FindControl("fname") as TextBox).Text;
                string laname = (item.FindControl("lname") as TextBox).Text;
                string address = (item.FindControl("address") as TextBox).Text;
                string email = (item.FindControl("email") as TextBox).Text;
                //string city = (item.FindControl("cityid") as DropDownList).SelectedValue;
                string city = Request.Form["cityid"];
                //string city = (item.FindControl("cityid") as TextBox).Text;
                string number = (item.FindControl("number") as TextBox).Text;
                string sql = "update task set firstname='" + fname + "' ,lastname='" + laname + "' ,email='"+email+"',address='" + address + "' ,cityid='" + city + "' ,number='" + number + "' where id='" + id + "' ";
                con.Open();
                MySqlCommand cmd = new MySqlCommand(sql, con);
                int status = cmd.ExecuteNonQuery();
                if (status > 0)
                {
                    Response.Redirect("About.aspx");
                }
                else
                {
                    Response.Redirect("Default.aspx");
                }

            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
            finally
            {
                con.Close();
            }

        }
        protected void ItemBound(object sender, RepeaterItemEventArgs args)
        {
            string id = Request.QueryString["id"];
            string sql = "select * from city_fk  where cityid='" + id + "',";
            MySqlDataAdapter sda = new MySqlDataAdapter(sql, con);
            DataTable dt = new DataTable();
            sda.Fill(dt);
            if (args.Item.ItemType == ListItemType.Item || args.Item.ItemType == ListItemType.AlternatingItem)
            {
                Repeater childRepeater = (Repeater)args.Item.FindControl("cityrptCustomers");
                childRepeater.DataSource = dt;
                childRepeater.DataBind();
            }
        }
        protected void childItemBound(object sender, RepeaterItemEventArgs args)
        {
            MySqlDataAdapter sda = new MySqlDataAdapter("select * from city_fk", con);
            DataTable dt = new DataTable();
            sda.Fill(dt);
            if (args.Item.ItemType == ListItemType.Item || args.Item.ItemType == ListItemType.AlternatingItem)
            {
                DropDownList childRepeater = (DropDownList)args.Item.FindControl("citydrop");
                int ID = (int)((DataRowView)args.Item.DataItem)["cityid"];
                childRepeater.DataSource = dt;
                childRepeater.DataValueField = "cityid";
                childRepeater.DataTextField = "city";
                childRepeater.SelectedValue = ID.ToString();
                childRepeater.DataBind();
            }
        }

    }
}

<%@ Page Language="C#" AutoEventWireup="true" EnableEventValidation="false"  CodeBehind="edit.aspx.cs" Inherits="firstask.edit" %>
<!doctype html>
<html lang="en">
  <head>
  

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/css/bootstrap.min.css"/>

    <title>Hello, world!</title>
      <style type="text/css">
          .style1
          {
              height: 34px;
          }
      </style>
  </head>
  <body>
    <div class="Container">

     <div class="row">
    <div class="col-md-1"></div>
    <div class="col-md-5">
     <asp:Repeater ID="repeater2" runat="server" OnItemDataBound="ItemBound">
<ItemTemplate>
<div class="Container">
<form id="Form1" runat="server">
<div class="form-group ">
      <label for="inputEmail4">First Name</label>
      <asp:TextBox ID="fname" CssClass="form-control"  Text='<%#Eval("firstname") %>'  runat="server"></asp:TextBox>
</div>
<div class="form-group">
      <label for="inputEmail4">Last Name</label>
        <asp:TextBox ID="lname" CssClass="form-control" Text='<%#Eval("lastname") %>' runat="server"></asp:TextBox>
</div>
 <div class="form-group">
    <label for="inputAddress">Email</label>
   <asp:TextBox ID="email" CssClass="form-control mt-2 mb-3"  Text='<%#Eval("email") %>' runat="server"></asp:TextBox>
  </div>
 <div class="form-group">
    <label for="inputAddress">Address</label>
   <asp:TextBox ID="address" CssClass="form-control mt-2 mb-3"  Text='<%#Eval("address") %>' runat="server"></asp:TextBox>
  </div>
 <div class="form-group">
      <label for="inputZip">Mobile Number</label>
      <asp:TextBox ID="number" CssClass="form-control mb-3" Text='<%#Eval("number") %>' runat="server"></asp:TextBox>
    </div>
 <div class="form-group col-md-6 mt-2 mb-3">
      <label for="inputCity">City</label>
       <%--<select name="cityid">--%>
      <asp:Repeater id="cityrptCustomers" runat="server" OnItemDataBound="childItemBound">
        <ItemTemplate>
        <asp:DropDownList ID="citydrop"  runat="server" AutoPostBack="True">
            
        </asp:DropDownList>
           
           </ItemTemplate>
     </asp:Repeater>
     <%--</select>--%>

          </div>
 <asp:Button ID="Button1" CssClass="btn btn-primary" OnClick="update" runat="server" Text="Update" />
<button class="btn btn-success"><a href="About.aspx">GO to Home</a></button>
</form>
</div>
</ItemTemplate>

</asp:Repeater>
    </div>
    </div>

    </div>
   

    <!-- Option 1: jQuery and Bootstrap Bundle (includes Popper) -->
    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/jquery@3.5.1/dist/jquery.slim.min.js"></script>
    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/js/bootstrap.bundle.min.js"></script>

    <!-- Option 2: Separate Popper and Bootstrap JS -->
    
    
    <script type="text/javascript"  src="https://cdn.jsdelivr.net/npm/popper.js@1.16.1/dist/umd/popper.min.js"></script>
    <script type="text/javascript"  src="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/js/bootstrap.min.js"></script>
    <script type="text/javascript"  src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/sweetalert/2.1.2/sweetalert.min.js"></script>
   
  </body>
  
</html>


          


