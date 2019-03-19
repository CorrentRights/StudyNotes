QProcess Class

QProcess *myprocess = new QProcess(/*parent*/);
QString proname  =  ui->cmdLineEdit->text();
myprocess->start(proname/*, arg*/); 