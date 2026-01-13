using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Numerics;

namespace WindowsFormsApp1
{
    public partial class Form1 : Form
    {

        private double xMin = -2.0;
        private double xMax = 1.0;
        private double yMin = -1.5;
        private double yMax = 1.5;

        public Form1()
        {
            InitializeComponent();
            this.pictureBox1.Paint += PictureBox1_Paint;

            this.trackBarIterations.ValueChanged += TrackBarIterations_ValueChanged;

            trackBarIterations.Minimum = 10;
            trackBarIterations.Maximum = 200;
            trackBarIterations.Value = 50;

            labelIterations.Text = $"Iterații: {trackBarIterations.Value}";

            pictureBox1.Invalidate();
        }

        private void Form1_Load(object sender, EventArgs e)
        {

        }
        private void PictureBox1_Paint(object sender, PaintEventArgs e)
        {
            int width = pictureBox1.Width;
            int height = pictureBox1.Height;

            using (Bitmap bmp = new Bitmap(width, height))
            {
                int maxIterations = trackBarIterations.Value;

                double rangeX = xMax - xMin;
                double rangeY = yMax - yMin;

                for (int pixelX = 0; pixelX < width; pixelX++)
                {
                    for (int pixelY = 0; pixelY < height; pixelY++)
                    {
                        double cReal = xMin + (double)pixelX / width * rangeX;
                        double cImag = yMin + (double)pixelY / height * rangeY;

                        Complex c = new Complex(cReal, cImag);
                        Complex z = Complex.Zero;
                        int iteration = 0;

                        while (Complex.Abs(z) < 2.0 && iteration < maxIterations)
                        {
                            z = z * z + c;
                            iteration++;
                        }

                        Color color;
                        if (iteration == maxIterations)
                        {
                            color = Color.Black;
                        }
                        else
                        {
                            double logI = Math.Log((double)iteration) / Math.Log((double)maxIterations);
                            int hueValue = (int)(90 * logI);

                            int r = (int)(255 * (Math.Sin(hueValue * 0.1) * 0.5 + 0.5));
                            int g = (int)(255 * (Math.Cos(hueValue * 0.1) * 0.5 + 0.5));
                            int b = (int)(255 * (Math.Sin(hueValue * 0.1 + 2) * 0.5 + 0.5));

                            color = Color.FromArgb(r, g, b);
                        }

                        bmp.SetPixel(pixelX, pixelY, color);
                    }
                }

                e.Graphics.DrawImage(bmp, 0, 0);
            }
        }

        private void TrackBarIterations_ValueChanged(object sender, EventArgs e)
        {
            labelIterations.Text = $"Iterații: {trackBarIterations.Value}";

            pictureBox1.Invalidate();
        }

        private void label1_Click(object sender, EventArgs e)
        {

        }
    }
}
