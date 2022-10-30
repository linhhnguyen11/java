// Xử lí dữ liệu
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
            // connnect to database 'testdb'
            Connection conn = getCon(DB_URL, USER_NAME, PASSWORD);
            // crate statement
            Statement stmt = conn.createStatement();
            // get data from table 'student'
           rs = stmt.executeQuery("select * from tbsach");
            // show data
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
            // close connection
            conn.close();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
		return copyData;
	}
	
	public void deleteSA(int nam) {
		try {
            // connnect to database 'testdb'
            Connection conn = getCon(DB_URL, USER_NAME, PASSWORD);
            // crate statement
            Statement stmt = conn.createStatement();
            // get data from table 'student'
            stmt.executeUpdate("DELETE FROM tbsach WHERE NamXB="+nam);
            // close connection
            conn.close();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
	}
