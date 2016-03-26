# Pictorial-Sentiments
Final Year Degree Project
/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */

package filechooser;
import java.awt.BorderLayout;
import java.awt.Color;
import java.awt.GridBagConstraints;
import java.awt.GridBagLayout;
import java.awt.Image;
import java.awt.Insets;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.File;
import java.io.IOException;
import javax.swing.filechooser.*;
import javax.imageio.ImageIO;
import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.*;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JMenu;
import javax.swing.JMenuBar;
import javax.swing.JMenuItem;
import javax.swing.JPanel;
import javax.swing.SwingUtilities;

import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.*;
import javax.imageio.ImageIO;
import javax.swing.JFrame;
import java.awt.image.BufferedImage;
import java.awt.image.DataBufferByte;

import java.io.File;
import javax.imageio.ImageIO;
import java.sql.*;
import org.opencv.core.Core;
import org.opencv.core.CvType;
import org.opencv.core.Mat;
import org.opencv.core.Size;
import org.opencv.imgproc.Imgproc;
import org.opencv.imgcodecs.Imgcodecs;

import java.sql.*;
import org.opencv.core.Scalar;

/**
 *
 * @author Hannan Naseem
 */
public class Filechooser extends JPanel {

    /**
     * @param args the command line arguments
     */
    private Image backgroundImage;
     private JFrame      j;
    private JMenu       jmenu;
    private JMenuBar    jbar;
    private JMenuItem   jmi, jexit;
    private JPanel      jpanel, jpanelbar;
    private JButton     jpre, jnext;
    JLabel              image;
    ImageIcon           ic;
    Image               img;
    File file;
    String s;
    
    public static void main(String[] args) {
        // TODO code application logic here
        
        
        
        SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                Filechooser fc = new Filechooser();
                fc.Main();
                 try{
                   
                    
             }
             catch(Exception e){
                 System.out.println(e);
             }
            }
        });
       
    }
   
   public void Main() {
       
        j = new JFrame("Image Viewer");
        j.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        j.setLocationByPlatform(true);
        j.setLayout(new GridBagLayout());
        GridBagConstraints c = new GridBagConstraints();
        jpanel = new JPanel();
        jpanel.setLayout(new BorderLayout());
        image = new JLabel(" ");
        jpanel.add(image, BorderLayout.CENTER);

        c.anchor = GridBagConstraints.PAGE_START;
        c.fill = GridBagConstraints.HORIZONTAL;
        c.gridx = c.gridy = 0;
        c.gridwidth = 2;
        
        c.weighty = 0.1;
        c.ipady = 600;
        c.insets = new Insets(5, 5, 10, 5);
       
        j.add(jpanel, c);

        jpanelbar = new JPanel();
        jpanelbar.setLayout(new GridBagLayout());
        jpanelbar.setBackground(Color.red);

        GridBagConstraints x = new GridBagConstraints();
     

       
        jbar = new JMenuBar();
        jmenu = new JMenu("File");
        jmi = new JMenuItem("Open");
        jmi.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent ae) {
                JFileChooser fc = new JFileChooser();
                javax.swing.filechooser.FileFilter imageFilter = new FileNameExtensionFilter("Image files", ImageIO.getReaderFileSuffixes());
                fc.addChoosableFileFilter(imageFilter);
                 fc.setAcceptAllFileFilterUsed(false);
                fc.setMultiSelectionEnabled(true);
                int result = fc.showOpenDialog(null);
                if (result == JFileChooser.APPROVE_OPTION) {
                     file = fc.getSelectedFile();
                     //System.out.println(file.getAbsolutePath());
                      s = file.getAbsolutePath();
                            greyScale(s);
                try{
                     Class.forName("com.mysql.jdbc.Driver");
                  
                     String url = "jdbc:mysql://localhost:3306/picsentimentdb?zeroDateTimeBehavior=convertToNull [root on Default schema]";
                     
                     Connection con = DriverManager.getConnection("jdbc:mysql:///picsentimentdb","root","");
                      
                    Statement st = con.createStatement();
                   
                    String sql = "INSERT INTO images " + "(ImagePolarity,ImageText,ImagePath) Values ('nuetral','none','" + s + "')";
                   
                     int count = st.executeUpdate(sql);
                     System.out.println(count);
                }
                catch(Exception ex ){
                    System.out.println(ex);
                }
                    
                    try {
                        image.setIcon(new ImageIcon(ImageIO.read(file)));
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        });
        jexit = new JMenuItem("Exit");
        jexit.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent ae) {
                System.exit(0);
            }
        });
        jmenu.add(jmi);
        jmenu.add(jexit);
        jbar.add(jmenu);
        j.setJMenuBar(jbar);

//      j.setSize(800, 600);
        j.pack();
        j.setResizable(true);
        j.setVisible(true);
       
      
    }
    public void greyScale(String orignalFile){
        try {
         System.load("C:\\Users\\Hannan Naseem\\Downloads\\opencv-3.1.0\\opencv\\build\\java\\x64\\" + Core.NATIVE_LIBRARY_NAME + ".dll");
          
         File input = new File(orignalFile);
         BufferedImage imagetoGrey = ImageIO.read(input);	
         byte[] data = ((DataBufferByte)  imagetoGrey.getRaster().getDataBuffer()).getData();
         Mat mat = new Mat( imagetoGrey.getHeight(),  imagetoGrey.getWidth(), CvType.CV_8UC3);
         mat.put(0, 0, data);
         Mat mat1 = new Mat( imagetoGrey.getHeight(), imagetoGrey.getWidth(),CvType.CV_8UC1);
         Imgproc.cvtColor(mat, mat1, Imgproc.COLOR_RGB2GRAY);
       
         byte[] data1 = new byte[mat1.rows() * mat1.cols() * (int)(mat1.elemSize())];
         mat1.get(0, 0, data1);
         BufferedImage image1 = new BufferedImage(mat1.cols(),mat1.rows(), BufferedImage.TYPE_BYTE_GRAY);
         image1.getRaster().setDataElements(0, 0, mat1.cols(), mat1.rows(), data1);

         File ouptut = new File("grayscale.jpg");
         ImageIO.write(image1, "jpg", ouptut);
         
         GuassianImage(ouptut.getAbsolutePath());
            
         
      } catch (IOException e) {
         System.out.println("Error: " + e.getMessage());
      }
        
    }
    
    public void GuassianImage(String greyImg){
        try{
         System.load("C:\\Users\\Hannan Naseem\\Downloads\\opencv-3.1.0\\opencv\\build\\java\\x64\\" + Core.NATIVE_LIBRARY_NAME + ".dll");
         
         Mat source = Imgcodecs.imread(greyImg,Imgcodecs.CV_LOAD_IMAGE_COLOR);
         
         Mat destination = new Mat(source.rows(),source.cols(),source.type());
         Imgproc.GaussianBlur(source, destination,new Size(45,45), 0);
         Imgcodecs.imwrite("Gaussian45.jpg", destination);
        System.out.println();
        
         
         
      
      } 
        catch (Exception e) {
         System.out.println("Error: " + e.getMessage());
      }

        
        ThresholdImg();
    }
     public void ThresholdImg(){
         
          try{

         System.load("C:\\Users\\Hannan Naseem\\Downloads\\opencv-3.1.0\\opencv\\build\\java\\x64\\" + Core.NATIVE_LIBRARY_NAME + ".dll");
         Mat source = Imgcodecs.imread("grayscale.jpg",  Imgcodecs.CV_LOAD_IMAGE_COLOR);
         
         Mat destination = new Mat(source.rows(),source.cols(),source.type());

         destination = source;
         Imgproc.threshold(source,destination,127,255,Imgproc.THRESH_TOZERO);
        Imgcodecs.imwrite("ThreshZero.jpg", destination);
         
         Mat invertcolormatrix= new Mat(source.rows(),source.cols(), source.type(), new Scalar(255,255,255));

         Core.subtract(invertcolormatrix, source, source);
         Imgcodecs.imwrite("Invert.jpg", destination);
         
         
         
         
      }catch (Exception e) {
         System.out.println("error: " + e.getMessage());
      }
         
     }
    
}
