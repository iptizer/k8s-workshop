# Use BusyBox as the base image
FROM busybox

# Create a script that prints "hello"
RUN echo -e "#!/bin/sh \necho hello from the container" > /echo.sh

# Make sure the script is executable
RUN chmod +x /echo.sh

# Execute your script when the container starts
CMD ["/echo.sh"]
