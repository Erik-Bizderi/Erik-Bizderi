Scaffold-DbContext "Data Source=bizderi.database.windows.net;Initial Catalog=Sansz_ruhak;Persist Security Info=True;User ID=erik;Password=Paprikaskrumpli12." Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Context RuhaContext -NoPluralize -Force

private void Form2_FormClosing(object sender, FormClosingEventArgs e)
        {
            DialogResult szoveg = MessageBox.Show("Biztos ki akarsz lépni?", "Kilépés", MessageBoxButtons.YesNo);
            
            if (szoveg == DialogResult.No)
            {
                e.Cancel = true;
            }
        }

//panel1.Controls.Clear();
//            var uc_Arukeszlet = new UC_arukeszlet();
//            panel1.Controls.Add(uc_Arukeszlet);


//
using Excel = Microsoft.Office.Interop.Excel;
using System.Reflection;

Models.RuhaContext RuhaContext = new Models.RuhaContext();
        public UC_arukeszlet()
        {
            InitializeComponent();
        }

        Excel.Application xlApp;
        Excel.Workbook xlWB;
        Excel.Worksheet xlSheet;

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

        private void btn_excel_Click(object sender, EventArgs e)
        {
            Excel_iras();

        }

        private void Excel_iras()
        {
            xlApp = new Excel.Application();

            xlWB = xlApp.Workbooks.Add(Missing.Value);

            xlSheet = xlWB.ActiveSheet;

            Iras();

            xlApp.Visible = true;
            xlApp.UserControl = true;

        }

        void Iras()
        {
            string[] fejlécek = new string[] {
                "RuhaID",
                "Kromoszoma",
                "Ruha",
                "Color",
                "Price"
            };

            var ruhak_tabla = RuhaContext.Ruhak.ToList();

            object[,] Ruha_Tömb = new object[ruhak_tabla.Count(), fejlécek.Count()];

            for (int i = 0; i < ruhak_tabla.Count(); i++)
            {
                Ruha_Tömb[i, 0] = ruhak_tabla[i].RuhaId;
                Ruha_Tömb[i, 1] = ruhak_tabla[i].Kromoszoma;
                Ruha_Tömb[i, 2] = ruhak_tabla[i].Ruha;
                Ruha_Tömb[i, 3] = ruhak_tabla[i].Color;
                Ruha_Tömb[i, 4] = ruhak_tabla[i].Price;
            }

            int sorok_száma = Ruha_Tömb.GetLength(0);
            int oszlopok_száma = Ruha_Tömb.GetLength(1);

            Excel.Range Range = xlSheet.get_Range("A1", Type.Missing).get_Resize(sorok_száma, oszlopok_száma);
            Range.Value2 = Ruha_Tömb;

            Range.Columns.AutoFit();

            Excel.Range header = xlSheet.get_Range("A1", Type.Missing).get_Resize(1, 5);
            header.Font.Bold = true;
            header.Interior.Color = Color.Fuchsia;
        }

//

Models.RuhaContext RuhaContext = new Models.RuhaContext();

        public UC_eladas_felvitele()
        {
            InitializeComponent();
        }

        private void UC_eladas_felvitele_Load(object sender, EventArgs e)
        {
            var ruhadarab = from x in RuhaContext.Ruhak
                            select x;

            listBox_Ruhak.DataSource = ruhadarab.ToList();
            listBox_Ruhak.DisplayMember = "Ruha";
                            
        }

        private void btn_Add_Click(object sender, EventArgs e)
        {
            var selected_ruha = (Ruhak)listBox_Ruhak.SelectedItem;
            var selected_nap = txt_nap.Text;
            var selected_elado = txt_elado.Text;
            var selected_eladas_id = int.Parse(txt_eladas_id.Text);

            Eladas newEladas = new Eladas()
            {
                RuhaId = selected_ruha.RuhaId,
                Alkalmazott = selected_elado.ToString(),
                Day = selected_nap.ToString(),
                EladasId = selected_eladas_id
            };

            RuhaContext.Eladas.Add(newEladas);

            try
            {
                RuhaContext.SaveChanges();
            }

            catch (Exception ex)
            {
                MessageBox.Show(ex.Message);
            }

//


Models.RuhaContext RuhaContext = new Models.RuhaContext();
        public UC_eladott_keszlet()
        {
            InitializeComponent();
        }

        private void UC_eladott_keszlet_Load(object sender, EventArgs e)
        {
            Eladott_Ruha_Szures();

        }

        private void Eladott_Ruha_Szures()
        {
            var adott_nap = txt_day.Text;
            var adott_alkalmazott = txt_alkalmazott.Text;

            var eladott_ruha = from x in RuhaContext.Eladas
                               where x.Day.Contains(adott_nap)
                               where x.Alkalmazott.Contains(adott_alkalmazott)
                               select x;

            listBox_eladott_ruha.DataSource = eladott_ruha.ToList();
            listBox_eladott_ruha.DisplayMember = "RuhaID";
        }

        public void txt_day_TextChanged(object sender, EventArgs e)
        {
            Eladott_Ruha_Szures();
        }

        private void txt_alkalmazott_TextChanged(object sender, EventArgs e)
        {
            Eladott_Ruha_Szures();
        }

        private void btn_sztorno_Click(object sender, EventArgs e)
        {
            var kivalasztott_vasarlas = (Eladas)listBox_eladott_ruha.SelectedItem;
            var torlendo_vasarlas = (from x in RuhaContext.Eladas
                                     where x.EladasId == kivalasztott_vasarlas.EladasId
                                     select x).FirstOrDefault();

            DialogResult torli_e = MessageBox.Show("Biztosan sztornózza a vásárlást?", "Sztornó", MessageBoxButtons.YesNo);
            
            if (torli_e == DialogResult.Yes)
            {
                RuhaContext.Eladas.Remove(torlendo_vasarlas);
            }
