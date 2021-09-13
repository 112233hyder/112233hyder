% function spin = metropolis(spin, kT, J)
%     Our program
% end

and naming the file metropolis.m (the same name as the function). We can also write functions to initialize the matrix of spins (initSpins.m) calculate the mean energy (energyIsing.m) and mean magnetization (magnetizationIsing.m). Using these functions, our program becomes
numSpinsPerDim = 2^3;
probSpinUp = 0.5;
J  = 1;
kT = 1;
spin  = initSpins(numSpinsPerDim, probSpinUp);
spin  = metropolis(spin, kT, J);
Emean = energyIsing(spin, J);
Mmean = magnetizationIsing(spin);

With this simpler form of our program, we can more easily write a for-loop to sweep through temperatures to see how the energy and magnetization vary.
numSpinsPerDim = 2^3;
probSpinUp = 0.5;
J = 1;
% Temperatures to sample
numTemps = 2^9;
kTc = 2*J / log(1+sqrt(2)); % Curie temperature
kT = linspace(0, 2*kTc, numTemps);
% Preallocate to store results
Emean = zeros(size(kT));
Mmean = zeros(size(kT));
% Replace 'for' with 'parfor' to run in parallel with Parallel Computing Toolbox.
for tempIndex = 1 : numTemps
    spin = initSpins(numSpinsPerDim, probSpinUp);
    spin = metropolis(spin, kT(tempIndex), J);
    Emean(tempIndex) = energyIsing(spin, J);
    Mmean(tempIndex) = magnetizationIsing(spin);
end
plot(kT / kTc, Emean, '.');
hold on;
window = (2^-3)*numTemps - 1;
plot(kT / kTc, movmean(  Emean, window));
plot(kT / kTc, movmedian(Emean, window));
hold off;
title('Mean Energy Per Spin vs Temperature');
xlabel('kT / kTc');
ylabel('<E>');
grid on;
legend('raw',...
    [num2str(window) ' pt. moving mean'],...
    [num2str(window) ' pt. moving median'],...
    'Location', 'NorthWest');

plot(kT / kTc, Mmean, '.');
grid on;
title('Magnetization vs Temperature');
xlabel('kT / kTc');
ylabel('M');

plot(kT / kTc, abs(Mmean), '.');
hold on;
window = (2^-3)*numTemps - 1;
plot(kT / kTc, movmean(  abs(Mmean), window));
plot(kT / kTc, movmedian(abs(Mmean), window));
hold off;
title('Magnetization vs Temperature');
xlabel('kT / kTc');
ylabel('|M|');
grid on;
legend('raw',...
    [num2str(window) ' pt. moving mean'],...
    [num2str(window) ' pt. moving median'],...
    'Location', 'NorthEast');

