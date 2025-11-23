📌 2. 🗂 Estrutura do Banco de Dados (Modelo Lógico)
Entidades principais

clinic

patients

doctors

appointments

Relacionamentos

Uma clínica possui N médicos

Uma clínica possui N pacientes

Um paciente agenda N consultas

Um médico realiza N consultas

🧱 1. SCRIPT — CREATE TABLES (DDL)

CREATE TABLE clinics (
  id VARCHAR(50) PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  created_at DATETIME NOT NULL DEFAULT NOW(),
  updated_at DATETIME NOT NULL DEFAULT NOW() ON UPDATE NOW()
);

CREATE TABLE patients (
  id VARCHAR(50) PRIMARY KEY,
  clinic_id VARCHAR(50) NOT NULL,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) NOT NULL,
  phone_number VARCHAR(20) NOT NULL,
  sex ENUM('M', 'F') NOT NULL,
  created_at DATETIME NOT NULL DEFAULT NOW(),
  updated_at DATETIME NOT NULL DEFAULT NOW() ON UPDATE NOW(),
  FOREIGN KEY (clinic_id) REFERENCES clinics(id) ON DELETE CASCADE
);

CREATE TABLE doctors (
  id VARCHAR(50) PRIMARY KEY,
  clinic_id VARCHAR(50) NOT NULL,
  name VARCHAR(255) NOT NULL,
  avatar_image_url VARCHAR(255),
  specialty VARCHAR(255) NOT NULL,
  appointment_price_in_cents INT NOT NULL,
  available_from_week_day INT NOT NULL,
  available_to_week_day INT NOT NULL,
  available_from_time TIME NOT NULL,
  available_to_time TIME NOT NULL,
  created_at DATETIME NOT NULL DEFAULT NOW(),
  updated_at DATETIME NOT NULL DEFAULT NOW() ON UPDATE NOW(),
  FOREIGN KEY (clinic_id) REFERENCES clinics(id) ON DELETE CASCADE
);

CREATE TABLE appointments (
  id VARCHAR(50) PRIMARY KEY,
  clinic_id VARCHAR(50) NOT NULL,
  patient_id VARCHAR(50) NOT NULL,
  doctor_id VARCHAR(50) NOT NULL,
  date DATETIME NOT NULL,
  created_at DATETIME NOT NULL DEFAULT NOW(),
  updated_at DATETIME NOT NULL DEFAULT NOW() ON UPDATE NOW(),
  FOREIGN KEY (clinic_id) REFERENCES clinics(id) ON DELETE CASCADE,
  FOREIGN KEY (patient_id) REFERENCES patients(id) ON DELETE CASCADE,
  FOREIGN KEY (doctor_id) REFERENCES doctors(id) ON DELETE CASCADE
);

🧩 2. SCRIPT — INSERTS (DML)

-- INSERT clinics
INSERT INTO clinics (id, name, created_at, updated_at)
VALUES
  ('c1', 'Clínica Central', NOW(), NOW()),
  ('c2', 'Clínica Popular', NOW(), NOW());

-- INSERT patients
INSERT INTO patients (id, name, email, phone_number, sex, created_at, updated_at, clinic_id)
VALUES
  ('p1', 'Maria Silva', 'maria@email.com', '11988887777', 'F', NOW(), NOW(), 'c1'),
  ('p2', 'João Souza', 'joao@email.com', '11999996666', 'M', NOW(), NOW(), 'c1');

-- INSERT doctors
INSERT INTO doctors (
  id, clinic_id, name, avatar_image_url, specialty, 
  appointment_price_in_cents, available_from_week_day, available_to_week_day, 
  available_from_time, available_to_time, created_at, updated_at
)
VALUES
  ('d1', 'c1', 'Dr. Lucas Araújo', NULL, 'Cardiologia', 35000, 1, 5, '08:00', '17:00', NOW(), NOW()),
  ('d2', 'c1', 'Dra. Paula Mendes', NULL, 'Dermatologia', 25000, 2, 6, '10:00', '16:00', NOW(), NOW());

-- INSERT appointments
INSERT INTO appointments (id, patient_id, clinic_id, doctor_id, date, created_at, updated_at)
VALUES
  ('a1', 'p1', 'c1', 'd1', '2025-02-10 09:00:00', NOW(), NOW()),
  ('a2', 'p2', 'c1', 'd2', '2025-02-12 14:00:00', NOW(), NOW());


🔍 3. SCRIPT — SELECTS

-- 1. Pacientes da clínica c1
SELECT *
FROM patients
WHERE clinic_id = 'c1'
ORDER BY name;

-- 2. Agendamentos com nome do paciente e do médico
SELECT 
    a.id AS appointment_id,
    p.name AS patient,
    d.name AS doctor,
    a.date
FROM appointments a
JOIN patients p ON a.patient_id = p.id
JOIN doctors d ON a.doctor_id = d.id
ORDER BY a.date;

-- 3. Médicos especialistas em Dermatologia
SELECT name, specialty
FROM doctors
WHERE specialty = 'Dermatologia';

-- 4. Próximos agendamentos
SELECT *
FROM appointments
WHERE date > NOW()
ORDER BY date ASC
LIMIT 10;

✏ 4. SCRIPT — UPDATES

-- Atualizar telefone de paciente
UPDATE patients
SET phone_number = '11977775555'
WHERE id = 'p1';

-- Atualizar horário do médico
UPDATE doctors
SET available_from_time = '09:00'
WHERE id = 'd1';

-- Remarcar agendamento
UPDATE appointments
SET date = '2025-02-15 11:00:00'
WHERE id = 'a1';


🗑 5. SCRIPT — DELETE

-- Deletar agendamento
DELETE FROM appointments
WHERE id = 'a2';

-- Deletar paciente (somente se não tiver agendamentos)

DELETE FROM patients
WHERE id = 'p2';

-- Deletar médico (somente se não tiver agendamentos)
DELETE FROM doctors
WHERE id = 'd2';
