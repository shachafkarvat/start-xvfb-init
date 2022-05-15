## create init.rc script to start xvfb on boot in Ubuntu 

Workaround to allow GUI apps to use Xserver in headless machine (i.e. AWS EC2 GPU instance with no screen attached)

Assuming X11 and drivers already installed

* Add xvfb
    ```bash
    sudo apt-get install xvfb
    ```
* create the file ```/etc/init.d/xvfb``` origin[^origin]
    ```bash
    #!/bin/sh
    ### BEGIN INIT INFO
    # Provides: Xvfb
    # Required-Start: $local_fs $remote_fs
    # Required-Stop:
    # X-Start-Before:
    # Default-Start: 2 3 4 5
    # Default-Stop: 0 1 6
    # Short-Description: Loads X Virtual Frame Buffer
    ### END INIT INFO

    XVFB=/usr/bin/Xvfb
    XVFBARGS=":1 -screen 0 1024x768x24 -ac +extension GLX +render -noreset"
    PIDFILE=/var/run/xvfb.pid
    case "$1" in
    start)
        echo -n "Starting virtual X frame buffer: Xvfb"
        start-stop-daemon --start --quiet --pidfile $PIDFILE --make-pidfile --background --exec $XVFB -- $XVFBARGS
        echo "."
        ;;
    stop)
        echo -n "Stopping virtual X frame buffer: Xvfb"
        start-stop-daemon --stop --quiet --pidfile $PIDFILE
        echo "."
        ;;
    restart)
        $0 stop
        $0 start
        ;;
    *)
            echo "Usage: /etc/init.d/xvfb {start|stop|restart}"
            exit 1
    esac

    exit 0
    ```
* Set as executable:
  ```bash 
  sudo chmod 0755 /etx/init.d/xvfb
  ```
* Enable:
  ```bash
  sudo update-rc.d xvfb defaults
  ```

[^origin]: Origin: https://forums.developer.nvidia.com/t/running-a-user-program-on-boot/66259/5