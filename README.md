clc;
clear;
close all;

% Step 1: Load audio
[x, fs] = audioread('dsp project audio .wav');

% If stereo → convert to mono
if size(x,2) == 2
    x = mean(x,2);
end

% Step 2: Add noise
noise = 0.05 * randn(size(x));
noisy = x + noise;

% Step 3: FIR Filter Design
n = 100; % order
fc = 0.05; % normalized cutoff (0.2 to 0.1)
b_fir = fir1(n, fc); % FIR coefficients

% Apply FIR filter
fir_output = filter(b_fir, 1, noisy);

% Define a bandpass range (normalized between 0 and 1)
[b_iir, a_iir] = butter(8, [0.02 0.15], 'bandpass'); 
iir_output = filter(b_iir, a_iir, noisy);

% Step 5: Plot signals
figure;

subplot(4,1,1);
plot(x); title('Original Signal');

subplot(4,1,2);
plot(noisy); title('Noisy Signal');

subplot(4,1,3);
plot(fir_output); title('FIR Filter Output');

subplot(4,1,4);
plot(iir_output); title('IIR Filter Output');

% Step 6: FFT Analysis
figure;
N = length(x);
f = (0:N-1)*(fs/N);

fft_noisy = abs(fft(noisy));
fft_fir = abs(fft(fir_output));
fft_iir = abs(fft(iir_output));

plot(f, fft_noisy); hold on;
plot(f, fft_fir);
plot(f, fft_iir);
legend('Noisy','FIR','IIR');
title('Frequency Spectrum');
xlabel('Frequency');
ylabel('Magnitude');

% Step 7: Play audio
disp('Playing Noisy Signal...');
sound(noisy, fs);
pause(5);

disp('Playing FIR Output...');
sound(fir_output, fs);
pause(5);

disp('Playing IIR Output...');
sound(iir_output, fs);
