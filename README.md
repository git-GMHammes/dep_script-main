# Dep Script - Sistema de Integração de Recursos Humanos

Sistema de integração de múltiplos bancos de dados de RH utilizando API REST, desenvolvido para centralizar e consultar informações de funcionários ativos e inativos de diferentes empresas.

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Arquitetura](#arquitetura)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Configuração](#configuração)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [API Endpoints](#api-endpoints)
- [Autenticação JWT](#autenticação-jwt)
- [Exemplos de Uso](#exemplos-de-uso)
- [Frontend ReactJS](#frontend-reactjs)
- [Troubleshooting](#troubleshooting)

## 🎯 Sobre o Projeto

O **Dep Script** é uma solução que integra dois bancos de dados distintos (MySQL e PostgreSQL) contendo informações de recursos humanos. O sistema permite consultas unificadas de funcionários ativos e inativos através de uma API REST segura com autenticação JWT, além de fornecer exemplos de consumo utilizando ReactJS.

### Principais Funcionalidades

- ✅ Integração simultânea com MySQL e PostgreSQL
- ✅ API REST completa com CodeIgniter 4
- ✅ Autenticação e autorização via JWT
- ✅ Busca de funcionários por status (ativo/inativo)
- ✅ Filtros por empresa
- ✅ Interface ReactJS para consumo da API
- ✅ Documentação completa de endpoints

## 🚀 Tecnologias Utilizadas

### Backend

- **PHP 8.1+**
- **CodeIgniter 4.4+**
- **Firebase JWT** - Autenticação
- **MySQL 8.0+** - Banco de dados principal
- **PostgreSQL 14+** - Banco de dados secundário

### Frontend (Exemplos)

- **ReactJS 18+**
- **Axios** - Cliente HTTP
- **React Router** - Navegação
- **React Hook Form** - Gerenciamento de formulários

## 🏗️ Arquitetura

```
┌─────────────────┐
│   ReactJS App   │
│   (Frontend)    │
└────────┬────────┘
         │ HTTP/JWT
         ▼
┌─────────────────┐
│  CodeIgniter 4  │
│   (API REST)    │
└────┬───────┬────┘
     │       │
     ▼       ▼
┌────────┐ ┌──────────┐
│ MySQL  │ │PostgreSQL│
│  (RH1) │ │  (RH2)   │
└────────┘ └──────────┘
```

## 📦 Pré-requisitos

- PHP >= 8.1
- Composer
- MySQL >= 8.0
- PostgreSQL >= 14
- Node.js >= 18 (para exemplos ReactJS)
- Git

## 💻 Instalação

### 1. Clone o Repositório

```bash
git clone https://github.com/seu-usuario/dep_script-main.git
cd dep_script-main
```

### 2. Instale as Dependências do Backend

```bash
cd backend
composer install
```

### 3. Instale as Dependências do Frontend

```bash
cd ../frontend
npm install
```

## ⚙️ Configuração

### Backend - CodeIgniter 4

#### 1. Configure o arquivo `.env`

Copie o arquivo de exemplo:

```bash
cd backend
cp env .env
```

Edite o arquivo `.env`:

```env
#--------------------------------------------------------------------
# ENVIRONMENT
#--------------------------------------------------------------------

CI_ENVIRONMENT = development

#--------------------------------------------------------------------
# APP
#--------------------------------------------------------------------

app.baseURL = 'http://localhost:8080/'
app.indexPage = ''

#--------------------------------------------------------------------
# DATABASE MySQL (Principal)
#--------------------------------------------------------------------

database.default.hostname = localhost
database.default.database = rh_mysql
database.default.username = root
database.default.password = sua_senha
database.default.DBDriver = MySQLi
database.default.DBPrefix =
database.default.port = 3306

#--------------------------------------------------------------------
# DATABASE PostgreSQL (Secundário)
#--------------------------------------------------------------------

database.postgres.hostname = localhost
database.postgres.database = rh_postgres
database.postgres.username = postgres
database.postgres.password = sua_senha
database.postgres.DBDriver = Postgre
database.postgres.DBPrefix =
database.postgres.port = 5432

#--------------------------------------------------------------------
# JWT
#--------------------------------------------------------------------

JWT_SECRET = 'sua-chave-secreta-super-segura-aqui'
JWT_TIME_TO_LIVE = 3600
```

#### 2. Estrutura das Tabelas

**MySQL - Tabela `funcionarios`**

```sql
CREATE DATABASE rh_mysql;
USE rh_mysql;

CREATE TABLE funcionarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    matricula VARCHAR(20) UNIQUE NOT NULL,
    nome VARCHAR(200) NOT NULL,
    cpf VARCHAR(14) UNIQUE NOT NULL,
    email VARCHAR(150),
    cargo VARCHAR(100),
    departamento VARCHAR(100),
    empresa VARCHAR(100) NOT NULL,
    data_admissao DATE,
    data_desligamento DATE NULL,
    status ENUM('ativo', 'inativo') DEFAULT 'ativo',
    salario DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_status (status),
    INDEX idx_empresa (empresa)
);

-- Dados de exemplo
INSERT INTO funcionarios (matricula, nome, cpf, email, cargo, departamento, empresa, data_admissao, status, salario) VALUES
('MAT001', 'João Silva', '123.456.789-01', 'joao.silva@empresa.com', 'Desenvolvedor', 'TI', 'Empresa A', '2020-01-15', 'ativo', 5000.00),
('MAT002', 'Maria Santos', '987.654.321-02', 'maria.santos@empresa.com', 'Analista', 'RH', 'Empresa A', '2019-05-20', 'inativo', 4500.00),
('MAT003', 'Pedro Oliveira', '456.789.123-03', 'pedro.oliveira@empresa.com', 'Gerente', 'Vendas', 'Empresa B', '2018-03-10', 'ativo', 7000.00);
```

**PostgreSQL - Tabela `employees`**

```sql
CREATE DATABASE rh_postgres;

\c rh_postgres;

CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    employee_code VARCHAR(20) UNIQUE NOT NULL,
    full_name VARCHAR(200) NOT NULL,
    document VARCHAR(14) UNIQUE NOT NULL,
    email VARCHAR(150),
    position VARCHAR(100),
    department VARCHAR(100),
    company VARCHAR(100) NOT NULL,
    hire_date DATE,
    termination_date DATE NULL,
    status VARCHAR(20) DEFAULT 'active',
    salary DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_employees_status ON employees(status);
CREATE INDEX idx_employees_company ON employees(company);

-- Dados de exemplo
INSERT INTO employees (employee_code, full_name, document, email, position, department, company, hire_date, status, salary) VALUES
('EMP001', 'Ana Costa', '321.654.987-04', 'ana.costa@empresa.com', 'Designer', 'Marketing', 'Empresa C', '2021-06-01', 'active', 4800.00),
('EMP002', 'Carlos Mendes', '789.123.456-05', 'carlos.mendes@empresa.com', 'Contador', 'Financeiro', 'Empresa C', '2017-09-15', 'inactive', 5500.00),
('EMP003', 'Juliana Lima', '147.258.369-06', 'juliana.lima@empresa.com', 'Coordenadora', 'Operações', 'Empresa D', '2022-01-20', 'active', 6500.00);
```

## 📁 Estrutura do Projeto

```
dep_script-main/
├── backend/
│   ├── app/
│   │   ├── Config/
│   │   │   ├── Routes.php
│   │   │   └── Database.php
│   │   ├── Controllers/
│   │   │   ├── AuthController.php
│   │   │   └── EmployeeController.php
│   │   ├── Models/
│   │   │   ├── FuncionarioModel.php
│   │   │   └── EmployeeModel.php
│   │   ├── Filters/
│   │   │   └── JWTAuth.php
│   │   └── Libraries/
│   │       └── JWTHandler.php
│   ├── vendor/
│   ├── .env
│   └── composer.json
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Login.jsx
│   │   │   ├── EmployeeList.jsx
│   │   │   └── EmployeeFilter.jsx
│   │   ├── services/
│   │   │   └── api.js
│   │   ├── utils/
│   │   │   └── auth.js
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── package.json
│   └── vite.config.js
│
└── README.md
```

## 🔌 API Endpoints

### Autenticação

#### POST `/api/auth/login`

Realiza login e retorna token JWT

**Request:**

```json
{
  "username": "admin",
  "password": "senha123"
}
```

**Response:**

```json
{
  "status": true,
  "message": "Login realizado com sucesso",
  "data": {
    "token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
    "expires_in": 3600
  }
}
```

### Funcionários

#### GET `/api/employees`

Lista todos os funcionários de ambos os bancos

**Headers:**

```
Authorization: Bearer {token}
```

**Query Parameters:**

- `status` - Filtro por status (ativo/inativo)
- `empresa` - Filtro por empresa
- `source` - Filtro por fonte de dados (mysql/postgres/all)

**Response:**

```json
{
  "status": true,
  "data": {
    "mysql": [
      {
        "id": 1,
        "matricula": "MAT001",
        "nome": "João Silva",
        "cargo": "Desenvolvedor",
        "empresa": "Empresa A",
        "status": "ativo",
        "fonte": "mysql"
      }
    ],
    "postgres": [
      {
        "id": 1,
        "employee_code": "EMP001",
        "full_name": "Ana Costa",
        "position": "Designer",
        "company": "Empresa C",
        "status": "active",
        "fonte": "postgres"
      }
    ],
    "total": 2
  }
}
```

#### GET `/api/employees/active`

Lista apenas funcionários ativos

#### GET `/api/employees/inactive`

Lista apenas funcionários inativos

#### GET `/api/employees/{id}?source={mysql|postgres}`

Busca funcionário específico

## 🔐 Autenticação JWT

### Implementação do JWT Handler

**app/Libraries/JWTHandler.php**

```php
<?php

namespace App\Libraries;

use Firebase\JWT\JWT;
use Firebase\JWT\Key;
use Exception;

class JWTHandler
{
    private $key;
    private $exp;

    public function __construct()
    {
        $this->key = getenv('JWT_SECRET');
        $this->exp = getenv('JWT_TIME_TO_LIVE') ?: 3600;
    }

    public function encode(array $payload): string
    {
        $time = time();

        $token = [
            'iat' => $time,
            'exp' => $time + $this->exp,
            'data' => $payload
        ];

        return JWT::encode($token, $this->key, 'HS256');
    }

    public function decode(string $token)
    {
        try {
            return JWT::decode($token, new Key($this->key, 'HS256'));
        } catch (Exception $e) {
            return null;
        }
    }

    public function validateToken(string $token): bool
    {
        return $this->decode($token) !== null;
    }
}
```

### Filtro de Autenticação

**app/Filters/JWTAuth.php**

```php
<?php

namespace App\Filters;

use CodeIgniter\Filters\FilterInterface;
use CodeIgniter\HTTP\RequestInterface;
use CodeIgniter\HTTP\ResponseInterface;
use App\Libraries\JWTHandler;

class JWTAuth implements FilterInterface
{
    public function before(RequestInterface $request, $arguments = null)
    {
        $header = $request->getHeaderLine('Authorization');

        if (empty($header)) {
            return service('response')
                ->setJSON([
                    'status' => false,
                    'message' => 'Token não fornecido'
                ])
                ->setStatusCode(401);
        }

        $token = str_replace('Bearer ', '', $header);

        $jwt = new JWTHandler();

        if (!$jwt->validateToken($token)) {
            return service('response')
                ->setJSON([
                    'status' => false,
                    'message' => 'Token inválido ou expirado'
                ])
                ->setStatusCode(401);
        }
    }

    public function after(RequestInterface $request, ResponseInterface $response, $arguments = null)
    {
        // Não é necessário implementar
    }
}
```

### Controller de Autenticação

**app/Controllers/AuthController.php**

```php
<?php

namespace App\Controllers;

use CodeIgniter\RESTful\ResourceController;
use App\Libraries\JWTHandler;

class AuthController extends ResourceController
{
    protected $format = 'json';

    public function login()
    {
        $username = $this->request->getPost('username');
        $password = $this->request->getPost('password');

        // Validação simples (em produção, use hash de senha)
        if ($username === 'admin' && $password === 'senha123') {
            $jwt = new JWTHandler();

            $payload = [
                'id' => 1,
                'username' => $username,
                'role' => 'admin'
            ];

            $token = $jwt->encode($payload);

            return $this->respond([
                'status' => true,
                'message' => 'Login realizado com sucesso',
                'data' => [
                    'token' => $token,
                    'expires_in' => getenv('JWT_TIME_TO_LIVE')
                ]
            ]);
        }

        return $this->failUnauthorized('Credenciais inválidas');
    }
}
```

### Controller de Funcionários

**app/Controllers/EmployeeController.php**

```php
<?php

namespace App\Controllers;

use CodeIgniter\RESTful\ResourceController;
use App\Models\FuncionarioModel;
use App\Models\EmployeeModel;

class EmployeeController extends ResourceController
{
    protected $format = 'json';

    public function index()
    {
        $status = $this->request->getGet('status');
        $empresa = $this->request->getGet('empresa');
        $source = $this->request->getGet('source') ?? 'all';

        $data = [
            'mysql' => [],
            'postgres' => [],
            'total' => 0
        ];

        // Busca no MySQL
        if (in_array($source, ['all', 'mysql'])) {
            $funcionarioModel = new FuncionarioModel();

            if ($status) {
                $funcionarioModel->where('status', $status);
            }
            if ($empresa) {
                $funcionarioModel->where('empresa', $empresa);
            }

            $mysql_data = $funcionarioModel->findAll();

            foreach ($mysql_data as &$item) {
                $item['fonte'] = 'mysql';
            }

            $data['mysql'] = $mysql_data;
        }

        // Busca no PostgreSQL
        if (in_array($source, ['all', 'postgres'])) {
            $employeeModel = new EmployeeModel();

            $statusMap = ['ativo' => 'active', 'inativo' => 'inactive'];

            if ($status && isset($statusMap[$status])) {
                $employeeModel->where('status', $statusMap[$status]);
            }
            if ($empresa) {
                $employeeModel->where('company', $empresa);
            }

            $postgres_data = $employeeModel->findAll();

            foreach ($postgres_data as &$item) {
                $item['fonte'] = 'postgres';
            }

            $data['postgres'] = $postgres_data;
        }

        $data['total'] = count($data['mysql']) + count($data['postgres']);

        return $this->respond([
            'status' => true,
            'data' => $data
        ]);
    }

    public function active()
    {
        $this->request->setGlobal('get', ['status' => 'ativo']);
        return $this->index();
    }

    public function inactive()
    {
        $this->request->setGlobal('get', ['status' => 'inativo']);
        return $this->index();
    }

    public function show($id = null)
    {
        $source = $this->request->getGet('source') ?? 'mysql';

        if ($source === 'mysql') {
            $model = new FuncionarioModel();
        } else {
            $model = new EmployeeModel();
        }

        $data = $model->find($id);

        if (!$data) {
            return $this->failNotFound('Funcionário não encontrado');
        }

        $data['fonte'] = $source;

        return $this->respond([
            'status' => true,
            'data' => $data
        ]);
    }
}
```

### Models

**app/Models/FuncionarioModel.php**

```php
<?php

namespace App\Models;

use CodeIgniter\Model;

class FuncionarioModel extends Model
{
    protected $table = 'funcionarios';
    protected $primaryKey = 'id';
    protected $useAutoIncrement = true;
    protected $returnType = 'array';
    protected $useSoftDeletes = false;
    protected $allowedFields = [
        'matricula', 'nome', 'cpf', 'email', 'cargo',
        'departamento', 'empresa', 'data_admissao',
        'data_desligamento', 'status', 'salario'
    ];
    protected $useTimestamps = true;
    protected $createdField = 'created_at';
    protected $updatedField = 'updated_at';
}
```

**app/Models/EmployeeModel.php**

```php
<?php

namespace App\Models;

use CodeIgniter\Model;

class EmployeeModel extends Model
{
    protected $DBGroup = 'postgres';
    protected $table = 'employees';
    protected $primaryKey = 'id';
    protected $useAutoIncrement = true;
    protected $returnType = 'array';
    protected $useSoftDeletes = false;
    protected $allowedFields = [
        'employee_code', 'full_name', 'document', 'email',
        'position', 'department', 'company', 'hire_date',
        'termination_date', 'status', 'salary'
    ];
    protected $useTimestamps = true;
    protected $createdField = 'created_at';
    protected $updatedField = 'updated_at';
}
```

### Configuração de Rotas

**app/Config/Routes.php**

```php
<?php

use CodeIgniter\Router\RouteCollection;

/**
 * @var RouteCollection $routes
 */

// Rotas de autenticação (sem filtro JWT)
$routes->group('api/auth', function($routes) {
    $routes->post('login', 'AuthController::login');
});

// Rotas protegidas com JWT
$routes->group('api', ['filter' => 'jwtauth'], function($routes) {
    // Funcionários
    $routes->get('employees', 'EmployeeController::index');
    $routes->get('employees/active', 'EmployeeController::active');
    $routes->get('employees/inactive', 'EmployeeController::inactive');
    $routes->get('employees/(:num)', 'EmployeeController::show/$1');
});
```

## ⚛️ Frontend ReactJS

### Configuração do Axios

**src/services/api.js**

```javascript
import axios from "axios";

const api = axios.create({
  baseURL: "http://localhost:8080/api",
  headers: {
    "Content-Type": "application/json",
  },
});

// Interceptor para adicionar token
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem("token");
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Interceptor para tratar erros de autenticação
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem("token");
      window.location.href = "/login";
    }
    return Promise.reject(error);
  }
);

export default api;
```

### Utilitário de Autenticação

**src/utils/auth.js**

```javascript
export const auth = {
  setToken: (token) => {
    localStorage.setItem("token", token);
  },

  getToken: () => {
    return localStorage.getItem("token");
  },

  removeToken: () => {
    localStorage.removeItem("token");
  },

  isAuthenticated: () => {
    return !!localStorage.getItem("token");
  },
};
```

### Componente de Login

**src/components/Login.jsx**

```jsx
import React, { useState } from "react";
import { useNavigate } from "react-router-dom";
import api from "../services/api";
import { auth } from "../utils/auth";

function Login() {
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState("");
  const [loading, setLoading] = useState(false);
  const navigate = useNavigate();

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError("");
    setLoading(true);

    try {
      const response = await api.post("/auth/login", {
        username,
        password,
      });

      if (response.data.status) {
        auth.setToken(response.data.data.token);
        navigate("/employees");
      }
    } catch (err) {
      setError(err.response?.data?.message || "Erro ao fazer login");
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="login-container">
      <div className="login-card">
        <h2>Dep Script - Login</h2>
        <form onSubmit={handleSubmit}>
          <div className="form-group">
            <label>Usuário</label>
            <input
              type="text"
              value={username}
              onChange={(e) => setUsername(e.target.value)}
              placeholder="Digite seu usuário"
              required
            />
          </div>

          <div className="form-group">
            <label>Senha</label>
            <input
              type="password"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              placeholder="Digite sua senha"
              required
            />
          </div>

          {error && <div className="alert alert-error">{error}</div>}

          <button type="submit" disabled={loading}>
            {loading ? "Entrando..." : "Entrar"}
          </button>
        </form>
      </div>
    </div>
  );
}

export default Login;
```

### Componente de Listagem de Funcionários

**src/components/EmployeeList.jsx**

```jsx
import React, { useState, useEffect } from "react";
import api from "../services/api";

function EmployeeList() {
  const [employees, setEmployees] = useState({ mysql: [], postgres: [] });
  const [loading, setLoading] = useState(true);
  const [filters, setFilters] = useState({
    status: "",
    empresa: "",
    source: "all",
  });

  useEffect(() => {
    fetchEmployees();
  }, [filters]);

  const fetchEmployees = async () => {
    setLoading(true);
    try {
      const params = new URLSearchParams();
      if (filters.status) params.append("status", filters.status);
      if (filters.empresa) params.append("empresa", filters.empresa);
      if (filters.source) params.append("source", filters.source);

      const response = await api.get(`/employees?${params.toString()}`);

      if (response.data.status) {
        setEmployees(response.data.data);
      }
    } catch (error) {
      console.error("Erro ao buscar funcionários:", error);
    } finally {
      setLoading(false);
    }
  };

  const handleFilterChange = (field, value) => {
    setFilters((prev) => ({ ...prev, [field]: value }));
  };

  const renderEmployeeCard = (employee, source) => {
    const isMySQL = source === "mysql";

    return (
      <div key={`${source}-${employee.id}`} className="employee-card">
        <div className="employee-header">
          <h3>{isMySQL ? employee.nome : employee.full_name}</h3>
          <span
            className={`badge ${
              employee.status === "ativo" || employee.status === "active"
                ? "active"
                : "inactive"
            }`}
          >
            {employee.status === "ativo" || employee.status === "active"
              ? "Ativo"
              : "Inativo"}
          </span>
        </div>

        <div className="employee-info">
          <p>
            <strong>Matrícula:</strong>{" "}
            {isMySQL ? employee.matricula : employee.employee_code}
          </p>
          <p>
            <strong>Cargo:</strong>{" "}
            {isMySQL ? employee.cargo : employee.position}
          </p>
          <p>
            <strong>Empresa:</strong>{" "}
            {isMySQL ? employee.empresa : employee.company}
          </p>
          <p>
            <strong>Departamento:</strong>{" "}
            {isMySQL ? employee.departamento : employee.department}
          </p>
          <p>
            <strong>Email:</strong> {employee.email}
          </p>
          <div className="source-badge">{source.toUpperCase()}</div>
        </div>
      </div>
    );
  };

  if (loading) {
    return <div className="loading">Carregando funcionários...</div>;
  }

  return (
    <div className="employee-list-container">
      <h1>Lista de Funcionários</h1>

      <div className="filters">
        <select
          value={filters.status}
          onChange={(e) => handleFilterChange("status", e.target.value)}
        >
          <option value="">Todos os Status</option>
          <option value="ativo">Ativos</option>
          <option value="inativo">Inativos</option>
        </select>

        <input
          type="text"
          placeholder="Filtrar por empresa"
          value={filters.empresa}
          onChange={(e) => handleFilterChange("empresa", e.target.value)}
        />

        <select
          value={filters.source}
          onChange={(e) => handleFilterChange("source", e.target.value)}
        >
          <option value="all">Todos os Bancos</option>
          <option value="mysql">MySQL</option>
          <option value="postgres">PostgreSQL</option>
        </select>

        <button onClick={fetchEmployees}>Buscar</button>
      </div>

      <div className="results-summary">
        <p>Total: {employees.total || 0} funcionários encontrados</p>
        <p>
          MySQL: {employees.mysql?.length || 0} | PostgreSQL:{" "}
          {employees.postgres?.length || 0}
        </p>
      </div>

      <div className="employees-grid">
        {employees.mysql?.map((emp) => renderEmployeeCard(emp, "mysql"))}
        {employees.postgres?.map((emp) => renderEmployeeCard(emp, "postgres"))}
      </div>

      {employees.total === 0 && (
        <div className="no-results">
          Nenhum funcionário encontrado com os filtros selecionados.
        </div>
      )}
    </div>
  );
}

export default EmployeeList;
```

### App Principal

**src/App.jsx**

```jsx
import React from "react";
import { BrowserRouter, Routes, Route, Navigate } from "react-router-dom";
import Login from "./components/Login";
import EmployeeList from "./components/EmployeeList";
import { auth } from "./utils/auth";

function PrivateRoute({ children }) {
  return auth.isAuthenticated() ? children : <Navigate to="/login" />;
}

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/login" element={<Login />} />
        <Route
          path="/employees"
          element={
            <PrivateRoute>
              <EmployeeList />
            </PrivateRoute>
          }
        />
        <Route path="/" element={<Navigate to="/employees" />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

## 🚦 Como Executar

### Backend

```bash
cd backend
php spark serve
```

O servidor estará disponível em `http://localhost:8080`

### Frontend

```bash
cd frontend
npm run dev
```

O aplicativo estará disponível em `http://localhost:5173`

### Credenciais de Teste

- **Usuário:** admin
- **Senha:** senha123

## 📊 Exemplos de Requisições

### cURL

```bash
# Login
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"senha123"}'

# Listar todos os funcionários
curl -X GET http://localhost:8080/api/employees \
  -H "Authorization: Bearer SEU_TOKEN"

# Listar funcionários ativos
curl -X GET http://localhost:8080/api/employees/active \
  -H "Authorization: Bearer SEU_TOKEN"

# Filtrar por empresa
curl -X GET "http://localhost:8080/api/employees?empresa=Empresa%20A" \
  -H "Authorization: Bearer SEU_TOKEN"

# Buscar apenas do PostgreSQL
curl -X GET "http://localhost:8080/api/employees?source=postgres" \
  -H "Authorization: Bearer SEU_TOKEN"
```

### JavaScript (Fetch)

```javascript
// Login
const login = async () => {
  const response = await fetch("http://localhost:8080/api/auth/login", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      username: "admin",
      password: "senha123",
    }),
  });

  const data = await response.json();
  return data.data.token;
};

// Buscar funcionários
const getEmployees = async (token) => {
  const response = await fetch("http://localhost:8080/api/employees", {
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });

  return await response.json();
};
```

## 🔧 Troubleshooting

### Problema: Erro de conexão com o banco de dados

**Solução:** Verifique as credenciais no arquivo `.env` e certifique-se de que os serviços MySQL e PostgreSQL estão rodando.

```bash
# Verificar status do MySQL
sudo systemctl status mysql

# Verificar status do PostgreSQL
sudo systemctl status postgresql
```

### Problema: Token JWT inválido

**Solução:** Verifique se a chave JWT no `.env` está configurada corretamente e se o token não expirou.

### Problema: CORS bloqueado no frontend

**Solução:** Configure o CORS no CodeIgniter. Adicione em `app/Config/Filters.php`:

```php
public $globals = [
    'before' => [
        'cors'
    ],
];
```

Crie o filtro CORS em `app/Filters/Cors.php`:

```php
<?php

namespace App\Filters;

use CodeIgniter\Filters\FilterInterface;
use CodeIgniter\HTTP\RequestInterface;
use CodeIgniter\HTTP\ResponseInterface;

class Cors implements FilterInterface
{
    public function before(RequestInterface $request, $arguments = null)
    {
        header('Access-Control-Allow-Origin: *');
        header('Access-Control-Allow-Headers: X-API-KEY, Origin, X-Requested-With, Content-Type, Accept, Access-Control-Request-Method, Authorization');
        header('Access-Control-Allow-Methods: GET, POST, OPTIONS, PUT, DELETE');

        if ($request->getMethod() === 'options') {
            exit();
        }
    }

    public function after(RequestInterface $request, ResponseInterface $response, $arguments = null)
    {
        // Não necessário
    }
}
```

## 📝 Licença

Este projeto é de código aberto e está disponível sob a [Licença MIT](LICENSE).

## 👥 Contribuindo

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues e pull requests.

## 📧 Contato

Para dúvidas ou sugestões, entre em contato através das issues do repositório.

---

**Desenvolvido com ❤️ para integração de sistemas de RH**
