#!/bin/bash

# Create the root folder with the current date and time (AM/PM)
ROOT_DIR="GKI-KernelSU-$(date +'%Y-%m-%d-%I-%M-%p')"
echo "Creating root folder $ROOT_DIR..."
mkdir -p "$ROOT_DIR"
# Copy Files temp fix
cp -r ./common-android14-5.15 ./"$ROOT_DIR"
cp -r ./AnyKernel3 ./"$ROOT_DIR"
cd "$ROOT_DIR"

# Clone the repositories into the root folder
echo "Cloning repositories..."
#git clone https://github.com/TheWildJames/common-android14-5.15.git
#git clone https://github.com/TheWildJames/AnyKernel3.git -b common-android14-5.15

# Get the kernel
#echo "Compiling the kernel..."
cd ./common-android14-5.15
#repo init -u https://android.googlesource.com/kernel/manifest
#mv manifest_12557009.xml .repo/manifests
#repo init -m manifest_12557009.xml
#repo sync

# Remove GKI protected exports
#rm common/android/abi_gki_protected_exports_*

# Setup KernelSU
curl -LSs "https://raw.githubusercontent.com/tiann/KernelSU/main/kernel/setup.sh" | bash -

# Compile the kernel
#LTO=thin BUILD_CONFIG=common/build.config.gki.aarch64 build/build.sh
tools/bazel build --config=fast //common:kernel_aarch64_dist

exit 

# Copy Image.lz4
echo "Copying Image.lz4..."
cp ./out/arch/arm64/boot/Image.lz4 ../AnyKernel3/Image.lz4

# Navigate to the anykernel directory
echo "Navigating to SultanSU/anykernel directory..."
cd ../AnyKernel3

# Zip the files in the anykernel directory with new naming convention
ZIP_NAME="GKI_KernelSU_Zuma_$(date +'%Y_%m_%d').zip"
echo "Creating zip file $ZIP_NAME..."
zip -r "../$ZIP_NAME" ./*

# Move back to the root directory
cd ..

# Final confirmation
echo "Zip file created and placed in $ROOT_DIR."
echo "Build and packaging process complete."
