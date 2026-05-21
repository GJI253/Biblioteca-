import java.sql.*;
import java.util.Scanner;

public class BibliotecaJDBC {
    // Cambia estos 3 datos por los tuyos
    private static final String URL = "jdbc:mysql://localhost:3306/biblioteca_db";
    private static final String USER = "root";
    private static final String PASS = "tu_contraseña";

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int opcion;
        
        do {
            System.out.println("\n=== BIBLIOTECA DB ===");
            System.out.println("1. Agregar libro - INSERT");
            System.out.println("2. Ver todos los libros - SELECT");
            System.out.println("3. Eliminar libro por ID - DELETE + WHERE");
            System.out.println("4. Buscar por autor - SELECT + WHERE");
            System.out.println("0. Salir");
            System.out.print("Elige: ");
            opcion = sc.nextInt();
            sc.nextLine(); // Limpiar buffer
            
            switch (opcion) {
                case 1: agregarLibro(sc); break;
                case 2: consultarLibros(); break;
                case 3: eliminarLibro(sc); break;
                case 4: buscarPorAutor(sc); break;
            }
        } while (opcion != 0);
        sc.close();
    }

    // Pista 1: INSERT - Agregar datos
    public static void agregarLibro(Scanner sc) {
        System.out.print("Título: ");
        String titulo = sc.nextLine();
        System.out.print("Autor: ");
        String autor = sc.nextLine();
        
        String sql = "INSERT INTO libros (titulo, autor) VALUES (?, ?)";
        
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             PreparedStatement ps = con.prepareStatement(sql)) {
            
            ps.setString(1, titulo);
            ps.setString(2, autor);
            ps.executeUpdate();
            System.out.println(">> Libro agregado");
            
        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }

    // Pista 2: SELECT - Consultar datos
    public static void consultarLibros() {
        String sql = "SELECT * FROM libros";
        
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             Statement st = con.createStatement();
             ResultSet rs = st.executeQuery(sql)) {
            
            System.out.println("\n--- LISTA DE LIBROS ---");
            while (rs.next()) {
                System.out.println("ID: " + rs.getInt("id") + 
                                 " | Título: " + rs.getString("titulo") + 
                                 " | Autor: " + rs.getString("autor"));
            }
            
        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }

    // Pista 3 + 4: DELETE con WHERE - Eliminar usando condición
    public static void eliminarLibro(Scanner sc) {
        System.out.print("ID del libro a eliminar: ");
        int id = sc.nextInt();
        
        String sql = "DELETE FROM libros WHERE id = ?"; // Aquí usas WHERE
        
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             PreparedStatement ps = con.prepareStatement(sql)) {
            
            ps.setInt(1, id);
            int filas = ps.executeUpdate();
            
            if (filas > 0) {
                System.out.println(">> Libro eliminado");
            } else {
                System.out.println(">> No existe libro con ID " + id);
            }
            
        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
    
    // Extra: SELECT con WHERE - Consultar con condición
    public static void buscarPorAutor(Scanner sc) {
        System.out.print("Nombre del autor: ");
        String autor = sc.nextLine();
        
        String sql = "SELECT * FROM libros WHERE autor = ?"; // WHERE aquí también
        
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             PreparedStatement ps = con.prepareStatement(sql)) {
            
            ps.setString(1, autor);
            ResultSet rs = ps.executeQuery();
            
            System.out.println("\n--- LIBROS DE " + autor + " ---");
            boolean hayResultados = false;
            while (rs.next()) {
                hayResultados = true;
                System.out.println("ID: " + rs.getInt("id") + 
                                 " | Título: " + rs.getString("titulo"));
            }
            if (!hayResultados) System.out.println("No hay libros de ese autor");
            
        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
