
	private static String DB_URL = "jdbc:mysql://localhost:3306/dlsach";
    	private static String USER_NAME = "root";
    	private static String PASSWORD = "";
	public static Connection getCon(String dbURL, String userName, 
            String password) {
        	Connection conn = null;
		try {
		    Class.forName("com.mysql.jdbc.Driver");
		    conn = DriverManager.getConnection(dbURL, userName, password);
		    System.out.println("connect successfully!");
		} catch (Exception ex) {
		    System.out.println("connect failure!");
		    ex.printStackTrace();
		}
		return conn;
    	}
	
	public String[][] getSA() {
		ResultSet rs = null;
		String data[][] = new String[10][10];
		String[][] copyData = new String[10][5];
		try {
            Connection conn = getCon(DB_URL, USER_NAME, PASSWORD);
            Statement stmt = conn.createStatement();
           rs = stmt.executeQuery("select * from tbsach");
           int i = 0;
   		while(rs.next()) {
   			data[i] = new String[] {rs.getString(1), rs.getString(2), rs.getString(3),rs.getString(4)};
   			i++; 
   		}
   		
		for(int i1=0; i1<data.length; i1++) {
			Sach sach = new Sach();
			if(data[i1][0] != null) {
				sach.setMaS(Integer.parseInt(data[i1][0]));
				sach.setTenS(data[i1][1]);
				sach.setNamXB(Integer.parseInt(data[i1][2]));
				sach.setGiaB(Integer.parseInt(data[i1][3]));

				copyData[i1][0] = data[i1][0];
				copyData[i1][1] = data[i1][1];
				copyData[i1][2] = data[i1][2];
				copyData[i1][3] = data[i1][3];
				copyData[i1][4] = Float.toString(sach.ThanhTien());
			}
		}
            conn.close();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
		return copyData;
	}
	
	public void deleteSA(int nam) {
		try {
            --connnect to database 'testdb'
            Connection conn = getCon(DB_URL, USER_NAME, PASSWORD);
            --crate statement
            Statement stmt = conn.createStatement();
            -- data from table 'student'
            stmt.executeUpdate("DELETE FROM tbsach WHERE NamXB="+nam);
            --ose connection
            conn.close();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
	}
	public boolean updateCB(String hoten, String diachi, String gioitinh) {
		try {
            --nnnect to database 'testdb'
            Connection conn = getCon(DB_URL, USER_NAME, PASSWORD);
            String SQL = "INSERT INTO tbCanbo (hoten, diachi, gioitinh)" + "VALUES(?, ?, ?)";
            PreparedStatement pstmt = conn.prepareStatement(SQL,
                    Statement.RETURN_GENERATED_KEYS);
            pstmt.setString(1, hoten);
            pstmt.setString(2, diachi);
            pstmt.setString(3, gioitinh);
            int id = pstmt.executeUpdate();
            if(id == 1) {
            	return true;
            }
            conn.close();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
		return false;
	}



GIAO DIỆN XOÁ
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.ResultSet;
import java.util.Arrays;

import javax.swing.JButton;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JOptionPane;
import javax.swing.JScrollPane;
import javax.swing.JTable;
import javax.swing.table.DefaultTableModel;

public class GUI_delSA {
	JFrame f;
	DefaultTableModel tableModel = new DefaultTableModel();
	JButton delSA = new JButton("Xoá sách");
	String strNamXB[] = {"2019", "2020", "2021"};
	JComboBox NamXB = new JComboBox(strNamXB);
	GUI_delSA() {
		XLSach listSach = new XLSach();
		String[][] data = listSach.getSA();
		NamXB.setBounds(100, 50, 150, 20);
		delSA.setBounds(350, 350, 100, 20);
		JTable tableSA = new JTable(tableModel);
		tableModel.addColumn("ID");
	    tableModel.addColumn("Tên sách");
	    tableModel.addColumn("Năm xuất bản");
	    tableModel.addColumn("Giá");
	    tableModel.addColumn("Thành tiền");
	    for(int i=0; i<data.length; i++) {
			if(data[i][0] != null) {
				tableModel.addRow(data[i]);
			}
		}
		tableSA.setBounds(30, 100, 200, 300);
		JScrollPane sp = new JScrollPane(tableSA);
        
		f = new JFrame("DASach");
		f.add(NamXB);
		f.add(delSA);
		f.add(sp);
		f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        f.setSize(600, 600);
        f.setVisible(true);
        delSA.addActionListener(new ActionListener(){  
    		public void actionPerformed(ActionEvent e){  
    			int getNam = Integer.parseInt(NamXB.getSelectedItem().toString());
    			JOptionPane.showMessageDialog(f,
    				    getNam,
    				    "Inane warning",
    				    JOptionPane.QUESTION_MESSAGE); 
    			for (int i=0; i<tableModel.getRowCount()-1; i++) {
    				if (tableModel.getValueAt(i, 2)!=null &&((String)tableModel.getValueAt(i, 2)).equals(Integer.toString(getNam))  ) {
    					tableModel.removeRow(i);
    	            }
    			}
    			XLSach listSach = new XLSach();
    			listSach.deleteSA(getNam);
    			String[][] data = listSach.getSA();
    			
    		 }
    			
    	}); 
	}
	
	 
	
}


GIAO DIỆN THÊM
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JTable;
import javax.swing.JTextField;
import javax.swing.border.EmptyBorder;

import java.awt.Component;
import java.awt.Rectangle;

import javax.swing.JButton;
import javax.swing.JCheckBox;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JOptionPane;
import javax.swing.JScrollPane;
import javax.swing.JTable;
import javax.swing.table.DefaultTableModel;

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.ItemEvent;
import java.awt.event.ItemListener;
import java.sql.ResultSet;
import java.util.Arrays;

public class GUI_Update extends JFrame {
	String hoten;
	String diachi;
	String gioitinh;
	JFrame f;
	DefaultTableModel tableModel = new DefaultTableModel();
	JButton delSA = new JButton("Thêm cán bộ");
	GUI_Update() {
		QLCB listSach = new QLCB();
		String[][] data = listSach.getSA();
		delSA.setBounds(350, 350, 150, 20);
		JTable tableSA = new JTable(tableModel);
		tableModel.addColumn("ID");
	    tableModel.addColumn("Họ tên");
	    tableModel.addColumn("Địa chỉ");
	    tableModel.addColumn("Giới tính");
	    for(int i=0; i<data.length; i++) {
			if(data[i][0] != null) {
				tableModel.addRow(data[i]);
			}
		}
		tableSA.setBounds(30, 100, 200, 300);
		JScrollPane sp = new JScrollPane(tableSA);
        
		JLabel lbname = new JLabel("Họ tên");
		JTextField ipname = new JTextField();
		JLabel lbdiachi = new JLabel("Địa chỉ");
		JTextField ipdiachi = new JTextField();
		JLabel lbgioitinh = new JLabel("Giới tính");
		JCheckBox cbnam = new JCheckBox("Nam",true);
		JCheckBox cbnu = new JCheckBox("Nữ");
		lbname.setBounds(20, 250, 100, 20);
		ipname.setBounds(150, 250, 100, 20);
		lbdiachi.setBounds(20, 300, 100, 20);
		ipdiachi.setBounds(150, 300, 100, 20);
		lbgioitinh.setBounds(20, 350, 100, 20);
		cbnam.setBounds(150, 350, 70, 20);
		cbnu.setBounds(230, 350, 70, 20);
		f = new JFrame("Cán Bộ");
		f.add(lbname);
		f.add(ipname);
		f.add(lbdiachi);
		f.add(ipdiachi);
		f.add(lbgioitinh);
		f.add(cbnam);
		f.add(cbnu);
		f.add(delSA);
		f.add(sp);
		f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        f.setSize(600, 600);
        f.setVisible(true);
        cbnam.addItemListener(new ItemListener() {
            public void itemStateChanged(ItemEvent e) {
                if (e.getStateChange() == 1) {
                	gioitinh = "Nam";
                	cbnu.setSelected(false);
                } 
            }
        }); 
		cbnu.addItemListener(new ItemListener() {
            public void itemStateChanged(ItemEvent e) {
                if (e.getStateChange() == 1) {
                	gioitinh = "Nữ";
                	cbnam.setSelected(false);
                } 
            }
        });
        delSA.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent e) {
				hoten = ipname.getText();
				diachi = ipdiachi.getText();
				
				System.out.println(gioitinh);
				QLCB canbo = new QLCB();
				canbo.updateCB(hoten, diachi, gioitinh);
				String[][] data = canbo.getSA();
				for(int i=data.length-1; i>0; i--) {
					if(data[i][0] != null) {
						tableModel.addRow(data[i]);
						break;
					}
				}
			}
		});
}
	
}
