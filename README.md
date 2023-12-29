clc
clear 
close all
sym x 
[FM0,t] = wave_signal();       %triangle wave from hw 3 exponatial form        

%FM Modulation
T = 0.01;
fm = 1/T;                       % data signal frequency
fc = 20*fm;                     % carrier signal frequency
wc = 2*pi*fc;                   % carrier signal rad frequency 
A = 2.5;                          % carrier signal amplitude
carrier = A*(cos(wc*t));          % carrier signal
delta = 500;                      % max deviation
B = delta/fm;              
intdata = cumtrapz(FM0)/fm;     % integral of data signal
FM1 = A*sin(wc*t+B*intdata);    % modulated signal 

%fm demod
demod = diff(FM1);              %deritive of signal to get data signal           
demod=[demod,demod(1,end)];     %Array of data
sigRec=abs(hilbert(demod));     %
dc=mean(sigRec);                %find average of signal
sigRec=sigRec-dc;               %dc offset to 0
sigRec = sigRec *9;             %amplifiy signal
FM2 = demod;                    
FM3 = sigRec;                   

%plot signal waveforms
figure(1)
subplot(4,1,1); 
plot(t(1:end),FM0(1:end))
title('Input Signal')
grid;
subplot(4,1,2); 
grid;
 plot(t,FM1);
title('FM Modulated Signal')
grid
subplot(4,1,3);
plot(t(1:end),FM2(1:end))
title('Demodulated Signal')
grid
subplot(4,1,4);
plot(t(1:end),FM3(1:end))
title('Filtered Signal')
grid

%FFT
L = 1000;
Fs = 1/0.012;
X = [FM0;FM1;FM2;FM3];       % array of all signals used for FFT
n = 2^nextpow2(L);
dim = 2;
Y = fft(X,n,dim);            %FFT for frequency spectrum
P2 = abs(Y/n);
P1 = P2(:,1:n/2+1);
P1(:,2:end-1) = 2*P1(:,2:end-1);

% polt frequency spectrums
figure(2)
subplot(4,1,1); 
plot(0:(Fs/n):(Fs/2-Fs/n),P1(1,1:n/2))
title('Frequency Spectrum of Input Signal')
grid
subplot(4,1,2); 
stem(0:(Fs/n):(Fs/2-Fs/n),P1(2,1:n/2))
title('Frequency Spectrum of FM Modulated Signal')
grid
subplot(4,1,3); 
plot(0:(Fs/n):(Fs/2-Fs/n),P1(3,1:n/2))
title('Frequency Spectrum of Demodulated Signal')
grid
subplot(4,1,4); 
plot(0:(Fs/n):(Fs/2-Fs/n),P1(4,1:n/2))
title('Frequency Spectrum of Filtered Signal')
grid
