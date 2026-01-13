using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace WindowsFormsApp1
{
    public partial class Form1 : Form
    {
        private Random random = new Random();
        private const int Width = 500;
        private const int Height = 500;
        private const int CloudRadius = 150;
        private const int CenterX = Width / 2;
        private const int CenterY = Height / 2;
        private const int NumSegments = 60;
        public Form1()
        {
            InitializeComponent();
            this.ClientSize = new Size(Width, Height);
            this.BackColor = Color.SkyBlue;
            this.pictureBox1.Size = this.ClientSize;
            this.pictureBox1.Paint += PictureBox1_Paint;
            this.pictureBox1.Invalidate();
        }
        private void PictureBox1_Paint(object sender, PaintEventArgs e)
        {
            Graphics g = e.Graphics;
            g.SmoothingMode = System.Drawing.Drawing2D.SmoothingMode.AntiAlias;
            Brush cloudBrush = new SolidBrush(Color.FromArgb(240, 255, 255, 255));
            Point[] cloudPoints = GenerateIrregularCloudPoints(CenterX, CenterY, CloudRadius, NumSegments);
            if (cloudPoints.Length > 2)
            {
                g.FillPolygon(cloudBrush, cloudPoints);
            }
            Pen softPen = new Pen(Color.FromArgb(100, 100, 100, 100), 2);
            if (cloudPoints.Length > 2)
            {
                g.DrawPolygon(softPen, cloudPoints);
            }
            cloudBrush.Dispose();
            softPen.Dispose();
        }

        private Point[] GenerateIrregularCloudPoints(int cx, int cy, int initialRadius, int segments)
        {
            List<Point> points = new List<Point>();
            double irregularityFactor = 0.2;
            int maxIrregularity = (int)(initialRadius * irregularityFactor);
            for (int i = 0; i < segments; i++)
            {
                double angle = (double)i / segments * 2 * Math.PI;
                int radialOffset = random.Next(-maxIrregularity, maxIrregularity);
                int currentRadius = initialRadius + radialOffset;
                int x = (int)(cx + currentRadius * Math.Cos(angle));
                int y = (int)(cy + currentRadius * Math.Sin(angle));
                points.Add(new Point(x, y));
            }
            return points.ToArray();
        }
        private void Form1_Load(object sender, EventArgs e)
        {

        }
    }
}