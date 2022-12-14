- 👋 Hi, I’m @Erik-Bizderi
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
Erik-Bizderi/Erik-Bizderi is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->


using Excel = Microsoft.Office.Interop.Excel;
using System.Reflection;

namespace Sansz_uzlet_2
{
    public partial class UC_arukeszlet : UserControl
    {
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
    }


}


        private void UC_arukeszlet_Load(object sender, EventArgs e)
        {
            ruhakBindingSource.DataSource = RuhaContext.Ruhak.ToList();
        }
    }
