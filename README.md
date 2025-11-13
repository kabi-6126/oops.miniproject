import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import dao.UserDAO;
import model.*;

public class LoginPage extends JFrame {

    private JTextField emailField;
    private JPasswordField passwordField;
    private JButton loginBtn;

    public LoginPage() {
        setTitle("Job Portal Login");
        setSize(600, 450);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        setLayout(null);
        setResizable(false);

        JPanel backgroundPanel = new JPanel() {
            int offset = 0;
            {

                Timer = new Timer(60, e -> {
                    offset = (offset + 4) % getWidth();
                    repaint();
                });
                timer.start();
            }

            @Override
            protected void paintComponent(Graphics g) {
                super.paintComponent(g);
                Graphics2D g2 = (Graphics2D) g;
                GradientPaint gp = new GradientPaint(
                        offset, 0, new Color(0, 180, 219), // blue-green start
                        getWidth(), getHeight(), new Color(0, 255, 200), // soft green end
                        true);
                g2.setPaint(gp);
                g2.fillRect(0, 0, getWidth(), getHeight());
            }
        };
        backgroundPanel.setBounds(0, 0, 600, 450);
        backgroundPanel.setLayout(null);
        add(backgroundPanel);

       JPanel card = new JPanel(null);
        card.setBounds(140, 80, 320, 280);
        card.setBackground(new Color(255, 255, 255, 230)); // translucent white
        card.setBorder(BorderFactory.createCompoundBorder(
                BorderFactory.createLineBorder(new Color(200, 200, 200), 1, true),
                BorderFactory.createEmptyBorder(15, 15, 15, 15)
        ));
        backgroundPanel.add(card);

        JLabel title = new JLabel("Welcome Back!", SwingConstants.CENTER);
        title.setFont(new Font("Segoe UI", Font.BOLD, 22));
        title.setForeground(new Color(33, 37, 41));
        title.setBounds(70, 15, 180, 40);
        card.add(title);

       JLabel emailIcon = new JLabel("📧");
        emailIcon.setFont(new Font("Segoe UI Emoji", Font.PLAIN, 20));
        emailIcon.setBounds(25, 70, 25, 25);
        card.add(emailIcon);

        emailField = new JTextField();
        styleTextField(emailField);
        emailField.setBounds(55, 70, 230, 30);
        emailField.setToolTipText("Enter your email");
        card.add(emailField);

    
        JLabel passIcon = new JLabel("🔒");
        passIcon.setFont(new Font("Segoe UI Emoji", Font.PLAIN, 20));
        passIcon.setBounds(25, 120, 25, 25);
        card.add(passIcon);

        passwordField = new JPasswordField();
        styleTextField(passwordField);
        passwordField.setBounds(55, 120, 230, 30);
        passwordField.setToolTipText("Enter your password");
        card.add(passwordField);

         loginBtn = new JButton("LOGIN");
        styleGradientButton(loginBtn,
                new Color(0, 180, 219), // blue-green gradient start
                new Color(0, 255, 200)); // gradient hover end
        loginBtn.setBounds(90, 180, 140, 40);
        card.add(loginBtn);

        JLabel footer = new JLabel("© 2025 Job Portal", SwingConstants.CENTER);
        footer.setForeground(Color.WHITE);
        footer.setFont(new Font("Segoe UI", Font.PLAIN, 13));
        footer.setBounds(0, 400, 600, 30);
        backgroundPanel.add(footer);

      loginBtn.addActionListener(e -> doLogin());

        setVisible(true);
    }
    private void styleTextField(JTextField field) {
        field.setFont(new Font("Segoe UI", Font.PLAIN, 14));
        field.setBackground(new Color(255, 255, 255, 230));
        field.setBorder(BorderFactory.createCompoundBorder(
                BorderFactory.createLineBorder(new Color(180, 180, 180), 1, true),
                BorderFactory.createEmptyBorder(6, 10, 6, 10)
        ));
    }

    
    private void styleGradientButton(JButton btn, Color base, Color hover) {
        btn.setFocusPainted(false);
        btn.setFont(new Font("Segoe UI", Font.BOLD, 15));
        btn.setForeground(Color.WHITE);
        btn.setBackground(base);
        btn.setBorder(BorderFactory.createEmptyBorder(10, 20, 10, 20));
        btn.setCursor(new Cursor(Cursor.HAND_CURSOR));

        btn.addMouseListener(new MouseAdapter() {
            public void mouseEntered(MouseEvent e) { btn.setBackground(hover.darker()); }
            public void mouseExited(MouseEvent e) { btn.setBackground(base); }
        });
    }

    private void doLogin() {
        String email = emailField.getText().trim();
        String password = new String(passwordField.getPassword()).trim();

        if (email.isEmpty() || password.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Please enter both email and password!",
                    "Missing Info", JOptionPane.WARNING_MESSAGE);
            return;
        }

        UserDAO dao = new UserDAO();
        User = dao.login(email, password);

        if (user != null) {
            JOptionPane.showMessageDialog(this, "Welcome " + user.getName() + "!");
            dispose();
            if (user instanceof Employer)
                new EmployerDashboard((Employer) user);
            else
                new JobSeekerDashboard((JobSeeker) user);
        } else {
            JOptionPane.showMessageDialog(this, "Invalid credentials!",
                    "Login Failed", JOptionPane.ERROR_MESSAGE);
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(LoginPage::new);
    }
}
