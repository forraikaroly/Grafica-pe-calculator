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
        private List<PointF> controlPoints = new List<PointF>();
        public Form1()
        {
            InitializeComponent();
            this.DoubleBuffered = true;
            this.Text = "Curba Bézier cubică - clic pentru puncte";
            this.BackColor = Color.White;
            this.ClientSize = new Size(800, 600);

        }
        protected override void OnMouseClick(MouseEventArgs e)
        {
            if (controlPoints.Count < 4)
            {
                controlPoints.Add(e.Location);
                Invalidate();
            }
            else
            {
                controlPoints.Clear();
                controlPoints.Add(e.Location);
                Invalidate();
            }
        }

        protected override void OnPaint(PaintEventArgs e)
        {
            base.OnPaint(e);
            Graphics g = e.Graphics;
            Pen penPoints = new Pen(Color.Gray, 1);
            Pen penCurve = new Pen(Color.Red, 2);

            if (controlPoints.Count > 0)
            {
                for (int i = 0; i < controlPoints.Count; i++)
                {
                    g.FillEllipse(Brushes.Blue, controlPoints[i].X - 3, controlPoints[i].Y - 3, 6, 6);
                    if (i > 0)
                        g.DrawLine(penPoints, controlPoints[i - 1], controlPoints[i]);
                }
            }

            if (controlPoints.Count == 4)
            {
                DrawCubicBezier(g, penCurve, controlPoints[0], controlPoints[1], controlPoints[2], controlPoints[3]);
            }
        }

        private void DrawCubicBezier(Graphics g, Pen pen, PointF p0, PointF p1, PointF p2, PointF p3)
        {
            const int steps = 100;
            PointF prev = p0;

            for (int i = 1; i <= steps; i++)
            {
                float t = i / (float)steps;
                PointF pt = CalculateBezierPoint(t, p0, p1, p2, p3);
                g.DrawLine(pen, prev, pt);
                prev = pt;
            }
        }

        private PointF CalculateBezierPoint(float t, PointF p0, PointF p1, PointF p2, PointF p3)
        {
            float x = (float)(
                Math.Pow(1 - t, 3) * p0.X +
                3 * Math.Pow(1 - t, 2) * t * p1.X +
                3 * (1 - t) * Math.Pow(t, 2) * p2.X +
                Math.Pow(t, 3) * p3.X);

            float y = (float)(
                Math.Pow(1 - t, 3) * p0.Y +
                3 * Math.Pow(1 - t, 2) * t * p1.Y +
                3 * (1 - t) * Math.Pow(t, 2) * p2.Y +
                Math.Pow(t, 3) * p3.Y);

            return new PointF(x, y);
        }
        private void Form1_Load(object sender, EventArgs e)
        {

        }
    }
}