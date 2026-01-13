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
        private readonly double[,] transforms = new double[,]
        {
            { 0.0, 0.0, 0.0, 0.16, 0.0, 0.0, 0.01 },    
            { 0.85, 0.04, -0.04, 0.85, 0.0, 1.6, 0.85 },  
            { 0.2, -0.26, 0.23, 0.22, 0.0, 1.6, 0.07 },  
            { -0.15, 0.28, 0.26, 0.24, 0.0, 0.44, 0.07 }  
        };
        private readonly Random rand = new Random();
        private double xMin = -3.0;
        private double xMax = 3.0;
        private double yMin = 0.0;
        private double yMax = 10.0;
        public Form1()
        {
            InitializeComponent();
            this.pictureBox1.Paint += PictureBox1_Paint;
            this.trackBarPoints.ValueChanged += TrackBarPoints_ValueChanged;
            trackBarPoints.Minimum = 1000;
            trackBarPoints.Maximum = 100000;
            trackBarPoints.Value = 10000;
            labelPoints.Text = $"Puncte: {trackBarPoints.Value}";
            pictureBox1.Invalidate();
        }
        private void PictureBox1_Paint(object sender, PaintEventArgs e)
        {
            int width = pictureBox1.Width;
            int height = pictureBox1.Height;
            if (width <= 0 || height <= 0) return;
            using (Bitmap bmp = new Bitmap(width, height))
            {
                int maxPoints = trackBarPoints.Value;
                double x = 0.0;
                double y = 0.0;
                double scaleX = width / (xMax - xMin);
                double scaleY = height / (yMax - yMin);
                int colorRange = 256 * 3;
                for (int i = 0; i < maxPoints; i++)
                {
                    int tIndex = GetRandomTransformIndex();
                    double a = transforms[tIndex, 0];
                    double b = transforms[tIndex, 1];
                    double c = transforms[tIndex, 2];
                    double d = transforms[tIndex, 3];
                    double e_x = transforms[tIndex, 4];
                    double f_y = transforms[tIndex, 5];
                    double x_new = a * x + b * y + e_x;
                    double y_new = c * x + d * y + f_y;
                    x = x_new;
                    y = y_new;
                    int px = (int)((x - xMin) * scaleX);
                    int py = (int)((yMax - y) * scaleY); 
                    if (px >= 0 && px < width && py >= 0 && py < height)
                    {
                        int colorValue = i % colorRange;
                        Color color;
                        if (colorValue < 256)
                        {
                            color = Color.FromArgb(0, colorValue, 0);
                        }
                        else if (colorValue < 256 * 2)
                        {
                            int rVal = colorValue - 256;
                            color = Color.FromArgb(rVal, 255, 0);
                        }
                        else
                        {
                            int bVal = colorValue - 256 * 2;
                            color = Color.FromArgb(255, 255, bVal > 255 ? 255 : bVal);
                        }
                        bmp.SetPixel(px, py, color);
                    }
                }
                e.Graphics.DrawImage(bmp, 0, 0);
            }
        }
        private int GetRandomTransformIndex()
        {
            double r = rand.NextDouble();
            double cumulativeProb = 0.0;

            for (int i = 0; i < transforms.GetLength(0); i++)
            {
                cumulativeProb += transforms[i, 6];
                if (r < cumulativeProb)
                {
                    return i;
                }
            }
            return transforms.GetLength(0) - 1;
        }
        private void TrackBarPoints_ValueChanged(object sender, EventArgs e)
        {
            labelPoints.Text = $"Puncte: {trackBarPoints.Value}";
            pictureBox1.Invalidate();
        }
        private void Form1_Load(object sender, EventArgs e)
        {

        }

        private void label1_Click(object sender, EventArgs e)
        {

        }
    }
}