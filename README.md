# signal-cli-on-Raspberry-PI---WORKS-
Instructions how to install signal-cli on Raspberry Pi - this one WORKS


## newest version is here: https://github.com/AsamK/signal-cli/releases
``export VERSION=0.11.6<br>
katalog_do_kompilowania_temp=/mnt/luks-raid1-16tb/temp
``
(
echo ; echo "VERSION = $VERSION" 
echo "katalog_do_kompilowania_temp = $katalog_do_kompilowania_temp" ; echo 
)

(
apt install -y curl zip protobuf-compiler clang libclang-dev cmake make
apt install -y openjdk-17-jdk
)

cd /opt
rm -v signal-cli-"${VERSION}"-Linux.tar.gz* /opt/signal-cli 2>/dev/null

wget https://github.com/AsamK/signal-cli/releases/download/v"${VERSION}"/signal-cli-"${VERSION}"-Linux.tar.gz
tar xf signal-cli-"${VERSION}"-Linux.tar.gz -C /opt
rm -v signal-cli-"${VERSION}"-Linux.tar.gz

(
ln -sf /opt/signal-cli-"${VERSION}"/bin/signal-cli /usr/local/bin/
ln -s /opt/signal-cli-"${VERSION}" /opt/signal-cli
ls -l /usr/local/bin/signal-cli /opt/signal-cli
)

curl https://sh.rustup.rs -sSf | sudo sh -s -- --default-toolchain nightly-aarch64-unknown-linux-gnu -y

export PATH=$PATH:$HOME/.cargo/bin

mkdir -p ${katalog_do_kompilowania_temp}/signal-cli-install && cd ${katalog_do_kompilowania_temp}/signal-cli-install
export LIBVERSION=$(find /opt/signal-cli-"${VERSION}"/lib/ -maxdepth 1 -mindepth 1 -name 'libsignal-client-*' | sed -E 's/\/opt\/signal-.*libsignal-client-//g' | sed -E 's/.jar//g')
echo ; echo "LIBVERSION = $LIBVERSION" ; echo 

wget https://github.com/signalapp/libsignal/archive/refs/tags/v"${LIBVERSION}".tar.gz ; echo $?
mv -v v"${LIBVERSION}".tar.gz ${katalog_do_kompilowania_temp}/signal-cli-install
tar xzf ${katalog_do_kompilowania_temp}/signal-cli-install/v"${LIBVERSION}".tar.gz -C ${katalog_do_kompilowania_temp}/signal-cli-install/ && mv ${katalog_do_kompilowania_temp}/signal-cli-install/libsignal-"${LIBVERSION}" libsignal
rm -v ${katalog_do_kompilowania_temp}/signal-cli-install/v"${LIBVERSION}".tar*
cd libsignal/java
sed -i "s/include ':android'//" ${katalog_do_kompilowania_temp}/signal-cli-install/libsignal/java/settings.gradle
${katalog_do_kompilowania_temp}/signal-cli-install/libsignal/java/build_jni.sh desktop
zip -d /opt/signal-cli-${VERSION}/lib/libsignal-client-*.jar libsignal_jni.so
zip /opt/signal-cli-${VERSION}/lib/libsignal-client-*.jar ${katalog_do_kompilowania_temp}/signal-cli-install/libsignal/target/release/libsignal_jni.so

mkdir -p /usr/java/packages/lib

cp -v ${katalog_do_kompilowania_temp}/signal-cli-install/libsignal/target/release/libsignal_jni.so /usr/java/packages/lib ; echo $?

rm -rv ${katalog_do_kompilowania_temp}/signal-cli-install


(
chown root:root /usr/java/packages/lib/libsignal_jni.so ; echo $?
chmod 755 /usr/java/packages/lib/libsignal_jni.so ; echo $?
chmod 755 -R /opt/signal-cli-${VERSION} ; echo $?
chown root:root -R /opt/signal-cli-${VERSION} ; echo $?
)

