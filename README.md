- 👋 Hi, I’m @Erik-Bizderi
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
Erik-Bizderi/Erik-Bizderi is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->


public partial class UC_arukeszlet : UserControl
    {
        Models.RuhaContext RuhaContext = new Models.RuhaContext();
        public UC_arukeszlet()
        {
            InitializeComponent();
        }

        private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
        {
            // listBox1.DataSource = RuhaContext.AruKeszlet.ToList();
            int rowindex = dataGridView1.CurrentCell.RowIndex;
            int columnindex = 0;

            var selected_ruha_id = dataGridView1.Rows[rowindex].Cells[columnindex].Value;

            var darabszam = from x in RuhaContext.AruKeszlet
                            where x.RuhaId.Equals(selected_ruha_id)
                            select x;

            listBox1.DataSource = darabszam.ToList();
            listBox1.DisplayMember = "Mennyiség";
        }

        private void UC_arukeszlet_Load(object sender, EventArgs e)
        {
            ruhakBindingSource.DataSource = RuhaContext.Ruhak.ToList();
        }
    }
