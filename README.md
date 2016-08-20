package medicaldocsdb;


import java.io.File;
import java.io.FileReader;
import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

import javax.servlet.ServletException;
import javax.servlet.annotation.MultipartConfig;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.Part;

import java.io.PrintWriter;
import java.util.Calendar;
import java.util.Random;

import javax.servlet.RequestDispatcher;

@WebServlet("/UploadFileServlet")
@MultipartConfig(maxFileSize = 16177215)

public class UploadFileServlet extends HttpServlet 
{
	
	 protected void doGet( HttpServletRequest request,
		        HttpServletResponse response ) throws ServletException, IOException
		    {
		        request.getRequestDispatcher( "client/uploadfile.jsp" ).forward(
		            request, response );
		    }
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException 
    {
                response.setContentType("text/html;charset=UTF-8");
                PrintWriter out = response.getWriter();
        
                InputStream inputStream = null;

                Random rand = new Random();
                int  n = rand.nextInt(9999) + 1;
                String idTemp=(String.valueOf(n));

                
                String title=request.getParameter("title");
                String category=request.getParameter("c_item");
                String subcategory=request.getParameter("sc_item");
                String type=request.getParameter("t_item");
                Part filePart = request.getPart("file_uploaded");
                String mydate=java.text.DateFormat.getDateTimeInstance().format(Calendar.getInstance().getTime());
                String date_create=request.getParameter("date");
                String notes=request.getParameter("notes");
                if (filePart != null) 
                {
                    System.out.println(filePart.getName());
                    System.out.println(filePart.getSize());
                    System.out.println(filePart.getContentType());

                    inputStream = filePart.getInputStream();
                }
        
                try{
                Connection c = null;
                String url = "jdbc:mysql://localhost:3306/medical_documents";
				String username = "root";
				String password = "";
				
                
               
                    String sql = "insert into upload_file (photo, title, category, subcategory, type, date, date_created, notes) values (?,?,?,?,?,?,?,?)";
                    
                    try {
        				Class.forName("com.mysql.jdbc.Driver");
        				
        				System.out.println("connection est");
        			} catch (ClassNotFoundException e) {
        				
        				System.out.println("failed");
        				// TODO Auto-generated catch block
        				e.printStackTrace();
        			} 
                    c = DriverManager.getConnection( url, username, password);
                  
                    PreparedStatement statement = c.prepareStatement(sql);
                  
                                        if (inputStream != null) 
                    {
                        statement.setBinaryStream(1, inputStream, (int) filePart.getSize());
                    }
                    statement.setString(2, title);
                    statement.setString(3, category);
                    statement.setString(4, subcategory);
                    statement.setString(5, type);
                    statement.setString(6, mydate);
                    statement.setString(7, date_create);
                    statement.setString(8, notes);
                    
                    System.out.println(title);
                    System.out.println(category);
                    System.out.println(subcategory);
                    System.out.println(type);
                    System.out.println(mydate);
                    System.out.println(date_create);
             
                  
                    
                    int row = statement.executeUpdate();
                    if (row > 0) 
                    {
                        out.println("File uploaded!!!");
                        
                        c.close();
                        
                        RequestDispatcher rs = request.getRequestDispatcher("client/uploadfile.jsp");
                        rs.include(request, response);
                    }
                    else
                    {
                        out.println("Couldn't upload your file!!!");
                        
                        c.close();
                        
                        RequestDispatcher rs = request.getRequestDispatcher("client/uploadfile.jsp");
                        rs.include(request, response);
                    }    

                }catch(Exception e){e.printStackTrace();}     
    }   
}
