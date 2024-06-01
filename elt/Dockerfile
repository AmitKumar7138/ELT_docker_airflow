FROM python:3.8-slim

# # Install PostgreSQL command-line tools
# RUN apt-get update && apt-get install -y postgresql-client-16
RUN apt-get update && \
    apt-get install -y wget gnupg2 lsb-release && \
    echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list && \
    wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - && \
    apt-get update && \
    apt-get install -y postgresql-client-16 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/


# Copy the ELT script 
COPY elt_script.py .

# Set the default command to run the ELT script
CMD ["python", "elt_script.py"]