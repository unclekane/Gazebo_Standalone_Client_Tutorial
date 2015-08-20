Gazebo standalone example
==========================

To create a standalone gazebo application with Qt GUI a QApplication and a gazebo intance have to be started. 

Qt Main Loop
------------
The main loop of Qt can be started with a QApplication. This have to be done in the main entrace of the application. 

Main.cpp:

#include "gazebo/gui/qt.h"
#include "gazebo/gazebo.hh"

#include <QApplication>

int main(int _argc, char **_argv)
{
  QApplication *app = new QApplication(g_argc, g_argv);
  
  //todo [A]: include GUI and gazebo
  
  int returnCode = app->exec(); // is entering in the main loop and returning on application exit
    
  return returnCode;
}


GUI and gazebo
--------------
Between entering the main loop and the QApplication definition the application logic have to be implemented. This can be the GUI and a gazebo client to communicate with the server. 

A example gui
-------------
The example class will have two buttons. One for starting a gazebo instance in seperate process and one for stoping this process via a termination signal. 

GUIWindow.hpp:
//The class will have two slots, which will called if the buttons was clicked. A QProcess pointer will hold the started gazebo server process and control is running behavior.

namespace gazebo
{
   class GAZEBO_VISIBLE GUIWindow : public QWidget
   {
      Q_OBJECT
      
      private: QProcess *server_process;
      
      protected slots: OnStartBtnClick();
      protected slots: OnStopBtnClick();
      
      public: GUIWindow();
   }
}


GUIWindow.cpp:
//In constructor a window and two buttons are defined and connected to the slots.

GUIWindow::GUIWindow()
{
    QWidget *Window = new QWidget();
    Window->setWindowTitle("GUI Main Window");
    
    QPushButton *startGazebo = new QPushButton(Window);
    startGazebo->setText("start");
    connect(startGazebo, SIGNAL(clicked()), this, SLOT(OnStartBtnClick()));
    
    QPushButton *stopGazebo = new QPushButton(Window);
    stopGazebo->setText("start");
    connect(stopGazebo, SIGNAL(clicked()), this, SLOT(OnStopBtnClick()));
}


// The slot will called if the start button was clicked. It will start a server instance in a seperate process. The process wil be a child process of the gui application.
GUIWindow::OnStartBtnClick()
{
    QStringList server_args;
    server_args.push_back( "--verbose" ); //add verbose argument to the server process
    
    server_process = new QProcess(this);
    server_process->setProcessChannelMode(QProcess::ForwardedChannels);     //the std outputs of the server are redirected to the std outputs of this application
    server_process->start("gzserver", server_args, QIODevice::Unbuffered);
    
    //todo [B]: start client
}

// If the stop button was clicked the server will be terminated and the process will wait 1000 ms until the server process finished and resume after the waiting time.
GUIWindow::OnStopBtnClick()
{
    server_process->terminate();
    server_process->waitForFinished(1000);
}

After the server is running a client instance is needed. This can be done with a seperate process or by running the client in a QThread. The example client will start a gazebo client and implement a dummy behavior in while loop:

GUIComClient.hpp:

namespace gazebo
{
    class GAZEBO_VISIBLE GUIComClient : public QThread
    {
        Q_OBJECT
        
        private: void run();
    }
}

GUIComClient.cpp:

void GUIComClient::run()
{
    // start minimal communication client
    gzLogInit("client-", "comm_client.log");

    if( !gazebo::setupClient(parent->argc, parent->argv) )
    {
        std::cerr << "Client could not be started!";
    }
    
    while(true)
      gazebo::common::Time::MSleep(10);     //replace with client application logic

    gazebo::shutdown();
    
}

The client can be started in GUIWindow.cpp in todo [A]: 
GUIWindow::OnStartBtnClick()
{
    ...
    //todo [A]:...
    GUIComClient *clientThread = new GUIComClient(this);
    clientThread->start();
}


The client is not terminated, this can be achieved by a signal and slot and a conditional argument in the while loop.
The example dummy application is ready now and can be started in the Main.cpp under todo [A]:

int main(int _argc, char **_argv)
{
  ...
  //todo [A]...
  gazebo::GUIWindow *window = new gazebo::GUIWindow();
  window->show();
  ...
}
  