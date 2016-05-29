---
layout: post
title:  "Basic chat client & server using .NET Framework's TcpClient and TcpListener"
date:   July 22, 2013
permalink: /basic-chat-client-server-using-net-frameworks-tcpclient-and-tcplistener/
comments: true
---

I considered getting rid of this bit of code while trying to determine whether it provided any real value to anyone. I've opted to keep it for future reference for myself and anyone else who may need an example of how the TcpClient and TcpListener classes can be utilized in .NET. The VS 2012 solution files can be cloned from it's [GitHub repo here](https://github.com/BrandonClapp/Basic-Chat-Example){:target="_blank"}. The files should be compatible with newer versions of Visual Studio as well.

Below is the logic behind the program, although for the full GUI you will need the full solution for the form designer files.

### Client Logic

```csharp
using System;
using System.IO;
using System.Net.Sockets;
using System.Windows.Forms;
using System.Threading;

namespace ChatClient
{
    public partial class ChatForm : Form
    {
        private TcpClient client;

        public ChatForm()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
        }

        private void ConnectButton_Click(object sender, EventArgs e)
        {
            string host = this.HostTextBox.Text;
            int port;
            if (int.TryParse(this.PortTextBox.Text, out port))
            {
                try
                {
                    client = new TcpClient(host, port);
                    StreamReader reader = new StreamReader(client.GetStream());
                    new Thread(() =>
                    {
                        while (true)
                        {
                            var line = reader.ReadLine();
                            this.ChatTextBox.Invoke(new MethodInvoker(() =>
                                this.ChatTextBox.Text += line + "\r\n"));
                        }
                    }).Start();
                }
                catch
                {
                    MessageBox.Show("Could not connect.");
                }
            }
        }

        private void SendTextBox_KeyDown(object sender, KeyEventArgs e)
        {
            if (e.KeyCode == Keys.Enter)
            {
                StreamWriter writer = new StreamWriter(client.GetStream());
                writer.WriteLine(this.UserNameTextBox.Text + ": " + this.SendTextBox.Text);
                this.SendTextBox.Text = "";
                writer.Flush();
            }
        }
    }
}
```

### Server Logic

```csharp
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Net.Sockets;
using System.Threading;

namespace ChatServer
{
    class Program
    {
        static void Main(string[] args)
        {
            List<TcpClient> clientsList = new List<TcpClient>();

            TcpListener listener = new TcpListener(IPAddress.Any, 8888);
            listener.Start();
            while (true)
            {
                TcpClient client = listener.AcceptTcpClient();
                clientsList.Add(client);
                new Thread(() =>
                {
                    try
                    {
                        StreamReader reader = new StreamReader(client.GetStream());
                        StreamWriter writer = new StreamWriter(client.GetStream());
                        writer.WriteLine("Welcome");
                        writer.Flush();

                        while (true)
                        {
                            string line = reader.ReadLine();
                            foreach (TcpClient c in clientsList)
                            {
                                StreamWriter ConnectedClientsWriter = new StreamWriter(c.GetStream());
                                ConnectedClientsWriter.WriteLine(line);
                                ConnectedClientsWriter.Flush();
                            }
                        }
                    }
                    catch
                    {
                        clientsList.Remove(client);
                    }
                }).Start();
            }
        }
    }
}
```
