using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace program_serial
{
    public partial class Form1 : Form
    {       
        public Form1()
        {
            InitializeComponent();           
        }

        private void button2_Click(object sender, EventArgs e)
        {
            try
            {
                serialPort1.Close();
                btnConnect.Enabled = true;
                btnDisconnect.Enabled = false;
                lblMessage.Text = serialPort1.PortName + "Closed";
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "WARNING", MessageBoxButtons.YesNo, MessageBoxIcon.Warning, MessageBoxDefaultButton.Button1);
            }
        }

        private void Form1_Load(object sender, EventArgs e)
        {          
            string[] portName = System.IO.Ports.SerialPort.GetPortNames();
            int[] baudrate = { 1800, 2400, 4800, 7200, 9600, 14400, 19200, 38400, 57600, 115200, 128000 }; 

            foreach (var port in portName)
            {
                cbbSerial.Items.Add(port);
            }
            foreach (var baud in baudrate)
            {
                cbbBaudrate.Items.Add(baud);
            }
            btnDisconnect.Enabled = false;
            serialPort1.DataReceived += new System.IO.Ports.SerialDataReceivedEventHandler(rctReceive_TextChanged);
        }

        private void btnConnect_Click(object sender, EventArgs e)
        {
            if (serialPort1.IsOpen)
            {
                serialPort1.Close();
            }
            try
            {
                serialPort1.PortName = Convert.ToString(cbbSerial.SelectedItem);
                serialPort1.BaudRate = Convert.ToInt32(cbbBaudrate.SelectedItem);
                serialPort1.Parity = System.IO.Ports.Parity.None;
                serialPort1.DataBits = 8;
                serialPort1.StopBits = System.IO.Ports.StopBits.One;
                btnConnect.Enabled = false;
                btnDisconnect.Enabled = true;
                lblMessage.Text = serialPort1.PortName + "connected";
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "WARNING", MessageBoxButtons.YesNo, MessageBoxIcon.Warning, MessageBoxDefaultButton.Button1);
            }
        }

        private void btnSend_Click(object sender, EventArgs e)
        {
            try
            {
                serialPort1.Write(txtSend.Text + Environment.NewLine);
                rctSend.AppendText(txtSend.Text + Environment.NewLine);
                rctSend.ScrollToCaret();
                txtSend.Text = string.Empty;
                
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "WARNING", MessageBoxButtons.YesNo, MessageBoxIcon.Warning, MessageBoxDefaultButton.Button1);
            }
        }

        private void rctReceive_TextChanged(object sender, EventArgs e)
        {
            rctReceive.BeginInvoke(new mydelegate(data));
        }
        public delegate void mydelegate();
        public void data()
        {
            rctReceive.AppendText(serialPort1.ReadExisting());
            rctReceive.ScrollToCaret();
        }

        private void Form1_FormClosing(object sender, FormClosingEventArgs e)
        {
            DialogResult respond = MessageBox.Show("Are you sure for closing this apps ?", "CONFIRMATION", MessageBoxButtons.YesNo, MessageBoxIcon.Question, MessageBoxDefaultButton.Button2);
            switch (respond)
            {
                case DialogResult.Yes:
                    e.Cancel = false;
                    break;
                case DialogResult.No:
                    e.Cancel = true;
                    break;
            }
        }     
    }
}
