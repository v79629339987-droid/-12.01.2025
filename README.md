# -12.01.2025
Тестовое задание 
src/api/greenApiClient.ts
import axios from 'axios';
import { config } from '../config/config';

export const greenApiClient = axios.create({
  baseURL: `${config.baseUrl}/waInstance${config.idInstance}`,
  headers: {
    'Content-Type': 'application/json',
  },
});
src/config/config.ts
import dotenv from 'dotenv';

dotenv.config();

export const config = {
  baseUrl: process.env.GREEN_API_URL!,
  idInstance: process.env.ID_INSTANCE!,
  apiToken: process.env.API_TOKEN_INSTANCE!,
  testPhone: process.env.TEST_PHONE!,
};
sendMessage.ts
import { greenApiClient } from './greenApiClient';
import { config } from '../config/config';

export const sendMessage = (phone: string, message: string) => {
  return greenApiClient.post(
    `/sendMessage/${config.apiToken}`,
    {
      chatId: `${phone}@c.us`,
      message,
    }
  );
};
getChatHistory.ts
import { greenApiClient } from './greenApiClient';
import { config } from '../config/config';

export const getChatHistory = (chatId: string) => {
  return greenApiClient.post(
    `/getChatHistory/${config.apiToken}`,
    {
      chatId,
      count: 10,
    }
  );
};
getStateInstance.ts
import { greenApiClient } from './greenApiClient';
import { config } from '../config/config';

export const getStateInstance = () => {
  return greenApiClient.get(
    `/getStateInstance/${config.apiToken}`
  );
};
sendMessage.test.ts
import { sendMessage } from '../src/api/sendMessage';
import { config } from '../src/config/config';

describe('sendMessage API', () => {
  it('200 OK – message sent', async () => {
    const response = await sendMessage(config.testPhone, 'QA test message');

    expect(response.status).toBe(200);
    expect(response.data).toHaveProperty('idMessage');
  });

  it('400 – missing message', async () => {
    try {
      // @ts-ignore
      await sendMessage(config.testPhone, '');
    } catch (error: any) {
      expect(error.response.status).toBe(400);
    }
  });
});
getChatHistory.test.ts
import { getChatHistory } from '../src/api/getChatHistory';
import { config } from '../src/config/config';

describe('getChatHistory API', () => {
  it('200 OK – chat history received', async () => {
    const response = await getChatHistory(`${config.testPhone}@c.us`);

    expect(response.status).toBe(200);
    expect(Array.isArray(response.data)).toBe(true);
  });

  it('400 – missing chatId', async () => {
    try {
      // @ts-ignore
      await getChatHistory('');
    } catch (error: any) {
      expect(error.response.status).toBe(400);
    }
  });
});
getStateInstance.test.ts
import { getStateInstance } from '../src/api/getStateInstance';

describe('getStateInstance API', () => {
  it('Instance is authorized', async () => {
    const response = await getStateInstance();

    expect(response.status).toBe(200);
    expect(response.data.stateInstance).toBe('authorized');
  });
});
Jest config (jest.config.ts)
export default {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testMatch: ['**/tests/**/*.test.ts'],
};

export default {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testMatch: ['**/tests/**/*.test.ts'],
};
README.md
## GREEN-API QA Test Task

### Stack
- TypeScript
- Jest
- Axios

### Install
npm install

### Configure
cp .env.example .env

### Run tests
npm test

