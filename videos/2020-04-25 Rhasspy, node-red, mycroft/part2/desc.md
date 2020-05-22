Privacy Aware Voice Assistant, now for real: integrating Mycroft, Rhasspy, and Node-Red - Part 2 (Rhasspy)

As you see I am fascinated by voice assistants, but I don't like them sending things into the cloud. Do I really want Google, Amazon, Apple, and Microsoft listen and analyze every word I say in my private four walls? How about voice assistants in an industrial setting - is it OK there to send everything to an external cloud provider? Not sure that such solutions will be easy to sell. And what happens when the Internet is down?

As excited as I initially was with Mycroft (https://mycroft.ai), I was a little disappointed to find out that they use (their own) third party cloud service to do speech to text. However, Mycroft is open source, so it allows me to exchange this service. After discovering Rhasspy (http://rhasspy.org, https://github.com/rhasspy/rhasspy-voltron, a system especially focusing on offline voice assistance).
I still like Mycroft and its community a lot, so what to do?

Hey, I am a software engineer, I thought: "I can glue systems together that don't have explicit interfaces for each other". So, let's use Node-RED (https://nodered.org) as glue!

Follow along in this mini-series

====

Manual: https://github.com/rhasspy/rhasspy-voltron

Dependencies:
on debian/ubuntu like follow manual [TODO: add]
for arch/majaro like: sudo pacman -S base-devel swig portaudio mosquitto sox espeak flite git \
    python-pyaudio supervisor patchelf lapack dpkg

git clone --recurse-submodules https://github.com/rhasspy/rhasspy-voltron
cd rhasspy-voltron/
make
scripts/install-opengrm.sh download/opengrm-1.3.4-amd64.tar.gz .venv/tools/kaldi
# (probably not necessary) with mc copy all bin and lib from opengrm to .venv/lib and tools
scripts/install-precise.sh download/precise-engine_0.3.0_amd64.tar.gz .venv/tools
scripts/install-phonetisaurus.sh download/phonetisaurus-2019-amd64.tar.gz .venv/tools # also necessary on pi

wget "http://mirrors.kernel.org/ubuntu/pool/universe/a/atlas/libatlas3-base_3.10.3-8ubuntu7_amd64.deb"
dpkg-deb -x libatlas3-base_3.10.3-8ubuntu7_amd64.deb libatlas
# copy libs to .venv/lib

bin/rhasspy-voltron --profile en



First part is here:
Third part here: 


====

Links to all projects:
- Mycroft: https://mycroft.ai)
- Rhasspy: http://rhasspy.org, https://github.com/rhasspy/rhasspy-voltron
- Node-RED: https://nodered.org

And don't forget to check these web-sites and my own Instagram account:
- ulno's site: https://ulno.net
- ulno's Instagram: https://www.instagram.com/ulno_net/
- IoTempire: https://iotempire.net

I am also now on lbry, check my channel there too:
lbry://@ulno#2
 
As usual, stay tuned and see you next time!

#makers #IoT #IoTempire #diy #arduino #raspberrypi #opensource #nodered #Node-RED #rhasspy #mycroft #mycroft.ai
